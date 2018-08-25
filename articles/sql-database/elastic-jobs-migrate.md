---
title: 新しい Elastic Database ジョブへの移行 | Microsoft Docs
description: 新しい Elastic Database ジョブに移行します。
services: sql-database
author: johnpaulkee
manager: craigg
ms.service: sql-database
ms.topic: article
ms.date: 06/14/2018
ms.author: johnpaulkee
ms.openlocfilehash: 7b6dd7d3724f486670b07709d317739d01e9fd21
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035308"
---
# <a name="migrate-to-the-new-elastic-database-jobs"></a>新しい Elastic Database ジョブへの移行

[Elastic Database ジョブ](elastic-jobs-overview.md)のバージョンがアップグレードされました。

カスタマー ホスト型の既存の [Elastic Database ジョブ](sql-database-elastic-jobs-overview.md)がある場合は、移行のコマンドレットとスクリプトを使用して最新バージョンに簡単に移行できます。


## <a name="prerequisites"></a>前提条件

アップグレード バージョンの Elastic Database ジョブには、移行時に使用する一連の新しい PowerShell コマンドレットがあります。 これらの新しいコマンドレットは、既存のジョブの資格情報、ターゲット (データベース、サーバー、カスタム コレクションを含む)、ジョブ トリガー、ジョブ スケジュール、ジョブ コンテンツ、およびジョブを新しいエラスティック ジョブ エージェントにすべて転送します。

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>最新のエラスティック ジョブ コマンドレットをインストールする

Azure サブスクリプションをまだお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

**AzureRM.Sql** 4.8.1-preview モジュールをインストールして、エラスティック ジョブの最新のコマンドレットを入手してください。 管理アクセス権で次の PowerShell コマンドを実行します。

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -RequiredVersion 4.8.1-preview -Force

# Import the AzureRM.Sql 4.8.1 module
Import-Module AzureRM.Sql -RequiredVersion 4.8.1

# Confirm if module successfully imported - if the imported version is 4.8.1, then continue
Get-Module AzureRM.Sql
```

### <a name="create-a-new-elastic-job-agent"></a>新しいエラスティック ジョブ エージェントを作成する

新しいコマンドレットをインストールしたら、新しいエラスティック ジョブ エージェントを作成します。

```powershell
# Register your subscription for the for the Elastic Jobs public preview feature
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Get an existing database to use as the job database - or create a new one if necessary
$db = Get-AzureRmSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName>
# Create a new elastic job agent
$agent = $db | New-AzureRmSqlElasticJobAgent -Name <agentName>
```

### <a name="install-the-old-elastic-database-jobs-cmdlets"></a>古い Elastic Database ジョブ コマンドレットをインストールする

移行を行うには*古い*エラスティック ジョブ コマンドレットがいくつか必要であるため、それらがまだインストールされていない場合は、次のコマンドを実行してください。

```powershell
# Install the old elastic job cmdlets if necessary and initialize the old jobs cmdlets
.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease

# Install the old jobs cmdlets
cd Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools
Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
.\InstallElasticDatabaseJobsCmdlets.ps1

# Choose the subscription where your existing jobs are
Select-AzureRmSubscription -SubscriptionId <subscriptionId>
Use-AzureSqlJobConnection -CurrentAzureSubscription -Credential (Get-Credential)
```



## <a name="migration"></a>移行

これで、古いエラスティック ジョブ コマンドレットと新しいエラスティック ジョブ コマンドレットが初期化されたので、ジョブの資格情報、ターゲット、およびジョブを新しい*ジョブ データベース*に移行します。

### <a name="setup"></a>セットアップ

```powershell
$ErrorActionPreference = "Stop";

# Helper function to show starting write output
function Log-StartOutput ($output) {
  Write-Output ("`r--------------------- " + $output + " ---------------------")
}

# Helper function to show starting write output
function Log-ChildOutput ($output) {
  Write-Output ("  - " + $output)
}
```



### <a name="migrate-credentials"></a>資格情報を移行する

```powershell
function Migrate-Credentials ($agent) {
    Log-StartOutput "Migrating credentials"

    $oldCreds = Get-AzureSqlJobCredential
    $oldCreds | % {
        $oldCredName = $_.CredentialName
        $oldUserName = $_.UserName
        Write-Output ("Credential " + $oldCredName)
        $oldCredential = Get-Credential -UserName $oldUserName `
                         -Message ("Please enter in the password that was used for your credential " + $oldCredName)
        try
        {
            $cred = New-AzureRmSqlElasticJobCredential -ParentObject $agent -Name $oldCredName -Credential $oldCredential
        }
        catch [System.Management.Automation.PSArgumentException]
        {
            $cred = Get-AzureRmSqlElasticJobCredential -ParentObject $agent -Name $oldCredName
            $cred = Set-AzureRmSqlElasticJobCredential -InputObject $cred -Credential $oldCredential
        }

        Log-ChildOutput ("Added user " + $oldUserName)
    }
}
```

資格情報を移行するには、前述の `$agent` PowerShell オブジェクトを渡すことによって次のコマンドを実行します。

```powershell
Migrate-Credentials $agent
```

サンプル出力

```powershell
# You should see similar output after executing the above
# --------------------- Migrating credentials ---------------------
# Credential cred1
#  - Added user user1
# Credential cred2
#  - Added user user2
# Credential cred3
#  - Added user user3
```

### <a name="migrate-targets"></a>ターゲットを移行する

```powershell
function Migrate-TargetGroups ($agent) {
    Log-StartOutput "Migrating target groups"

    # Setup hash of target groups
    $targetGroups = [ordered]@{}

    # Fetch root job targets from old service
    $rootTargets = Get-AzureSqlJobTarget

    # Return if no root targets are found
    if ($rootTargets.Count -eq 0)
    {
        Write-Output "No targets found - no need for migration"
        return
    }

    # Create list of target groups to create
    # We format the target group name as such:
    # - If root target is server type, then target group name is "(serverName)"
    # - If root target is database type, then target group name is "(serverName,databaseName)"
    # - If root target is shard map type, then target group name is "(serverName,databaseName,shardMapName)"
    # - If root target is custom collection, then target group name is "customCollectionName"
    $rootTargets | % {
        $tgName = Format-OldTargetName -target $_
        $childTargets = Get-ChildTargets -target $_
        $targetGroups.Add($tgName, $childTargets)
    }

    # Flatten list
    for ($i=$targetGroups.Count - 1; $i -ge 0; $i--)
    {
        # Fetch target group's initial list of targets unexpanded
        $targets = $targetGroups[$i]

        # Expand custom collection targets
        $j = 0;
        while ($j -lt $targets.Count)
        {
            $target = $targets[$j]
            if ($target.TargetType -eq "CustomCollection")
            {
                $targets = [System.Collections.ArrayList] $targets
                $targets.Remove($target) # Remove this target from the list

                $expandedTargets = $targetGroups[$target.TargetDescription.CustomCollectionName]

                foreach ($expandedTarget in $expandedTargets)
                {
                    $targets.Add($expandedTarget) | Out-Null
                }

                # Set updated list of targets for tg
                $targetGroups[$i] = $targets
                # Note we don't increment here in case we need to expand further
            }
            else
            {
                # Skip if no custom collection target needs to be expanded
                $j++
            }
        }
    }

    # Add targets to target group
    foreach ($targetGroup in $targetGroups.Keys)
    {
        $tg = Setup-TargetGroup -tgName $targetGroup -agent $agent
        $targets = $targetGroups[$targetGroup]
        Migrate-Targets -targets $targets -tg $tg
        $targetsAdded = (Get-AzureRmSqlElasticJobTargetGroup -ParentObject $agent -Name $tg.TargetGroupName).Targets
        foreach ($targetAdded in $targetsAdded)
        {
            Log-ChildOutput ("Added target " + (Format-NewTargetName $targetAdded))
        }
    }
}

## Target group helpers
# Migrate shard map target from old jobs to new job's target group
function Migrate-Targets ($targets, $tg) {
  Write-Output ("Target group " + $tg.TargetGroupName)
  foreach ($target in $targets) {
    if ($target.TargetType -eq "Server") {
      Add-ServerTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "Database") {
      Add-DatabaseTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "ShardMap") {
      Add-ShardMapTarget -target $target -tg $tg
    }
  }
}

# Migrate server target from old jobs to new job's target group
function Add-ServerTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $credName = $jobTarget.MasterDatabaseCredentialName
  $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -RefreshCredentialName $credName
}

# Migrate database target from old jobs to new job's target group
function Add-DatabaseTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $databaseName = $jobTarget.DatabaseName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName -Exclude
  }
  else {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName
  }
}

# Migrate shard map target from old jobs to new job's target group
function Add-ShardMapTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $smName = $jobTarget.ShardMapName
  $serverName = $jobTarget.ShardMapManagerServerName
  $databaseName = $jobTarget.ShardMapManagerDatabaseName
  $credName = $jobTarget.ShardMapManagerCredentialName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName -Exclude
  }
  else {
    $t = Add-AzureRmSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName
  }
}

# Helper to format target old target names
function Format-OldTargetName ($target) {
  if ($target.TargetType -eq "Server") {
    $tgName = "(" + $target.ServerName + ")"
  }
  elseif ($target.TargetType -eq "Database") {
    $tgName = "(" + $target.ServerName + "," + $target.DatabaseName + ")"
  }
  elseif ($target.TargetType -eq "ShardMap") {
    $tgName = "(" + $target.ShardMapManagerServerName + "," +
    $target.ShardMapManagerDatabaseName + "," + `
      $target.ShardMapName + ")"
  }
  elseif ($target.TargetType -eq "CustomCollection") {
    $tgName = $target.CustomCollectionName
  }

  return $tgName
}

# Helper to format new target names
function Format-NewTargetName ($target) {
  if ($target.TargetType -eq "SqlServer") {
    $tgName = "(" + $target.TargetServerName + ")"
  }
  elseif ($target.TargetType -eq "SqlDatabase") {
    $tgName = "(" + $target.TargetServerName + "," + $target.TargetDatabaseName + ")"
  }
  elseif ($target.TargetType -eq "SqlShardMap") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetShardMapName + ")"
  }
  elseif ($target.TargetType -eq "SqlElasticPool") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetElasticPoolName + ")"
  }

  return $tgName
}

# Get child targets
function Get-ChildTargets($target) {
  if ($target.TargetType -eq "CustomCollection") {
    $children = Get-AzureSqlJobChildTarget -TargetId $target.TargetId
    if ($children.Count -eq 1)
    {
        $arr = New-Object System.Collections.ArrayList($null)
        $arr.Add($children)
        $children = $arr
    }
    return $children
  }
  else {
    return $target
  }
}

# Migrates target groups
function Setup-TargetGroup ($tgName, $agent) {
  try {
    $tg = New-AzureRmSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
  catch [System.Management.Automation.PSArgumentException] {
    $tg = Get-AzureRmSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
}
```

ターゲット (サーバー、データベース、およびカスタム コレクション) を新しいジョブ データベースに移行するには、**Migrate-TargetGroups** コマンドレットを実行して次の操作を行います。

- ルート レベルのターゲット (サーバーとデータベース) は、ルート レベルのターゲットだけを格納する "(\<serverName\>, \<databaseName\>)" という名前の新しいターゲット グループに移行されます。
- カスタム コレクションは、すべての子ターゲットを格納する新しいターゲット グループに移行されます。

```powershell
Migrate-TargetGroups $agent
```

サンプル出力:

```powershell
# --------------------- Migrating target groups ---------------------
# Target group cc1
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group cc2
#   - Added target (s1,db1)
# Target group cc3
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group (s1,db1)
#   - Added target (s1,db1)
# Target group (s1,db2)
#   - Added target (s1,db2)
# Target group (s1)
#   - Added target (s1)
# Target group (s1,db1,sm1)
#   - Added target (s1,db1,sm1)
```



### <a name="migrate-jobs"></a>ジョブを移行する

```powershell
function Migrate-Jobs ($agent)
{
    Log-StartOutput "Migrating jobs and job steps"

    $oldJobs = Get-AzureSqlJob
    $newJobs = [System.Collections.ArrayList] @()

    foreach ($oldJob in $oldJobs)
    {
        # Ignore system jobs
        if ($oldJob.ContentName -eq $null)
        {
            continue
        }

        # Schedule
        $oldJobTriggers = Get-AzureSqlJobTrigger -JobName $oldJob.JobName

        if ($oldJobTriggers.Count -ge 1)
        {
            foreach ($trigger in $oldJobTriggers)
            {

                $schedule = Get-AzureSqlJobSchedule -ScheduleName $trigger.ScheduleName
                $newJob = [PSCustomObject] @{
                    JobName  = ($trigger.JobName + " (" + $trigger.ScheduleName + ")");
                    Description = $oldJob.ContentName
                    Schedule = $schedule
                    TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                    CredentialName = $oldJob.CredentialName
                    Output = $oldJob.ResultSetDestination
                }
                $newJobs.Add($newJob) | Out-Null
            }
        }
        else
        {
            $newJob = [PSCustomObject] @{
                JobName  = $oldJob.JobName
                Description = $oldJob.ContentName
                Schedule = $null
                TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                CredentialName = $oldJob.CredentialName
                Output = $oldJob.ResultSetDestination
            }
            $newJobs.Add($newJob) | Out-Null
        }
    }

    # At this point, we should have an organized list of jobs to create
    foreach ($newJob in $newJobs)
    {
        Write-Output ("Job " + $newJob.JobName)
        $job = Setup-Job $newJob $agent
        If ($job.Interval -ne $null)
        {
            Log-ChildOutput ("Schedule with start time " + $job.StartTime + " and end time at " + $job.EndTime)
            Log-ChildOutput ("Repeats every " + $job.Interval)
        }
        else {
            Log-ChildOutput ("Repeats once")
        }

        Setup-JobStep $newJob $job
    }
}

# Migrates jobs
function Setup-Job ($job, $agent) {
  $jobName = $newJob.JobName
  $jobDescription = $newJob.Description

  # Create or update a job has a recurring schedule
  if ($newJob.Schedule -ne $null) {
    $schedule = $newJob.Schedule
    $startTime = $schedule.StartTime.UtcTime
    $endTime = $schedule.EndTime.UtcTime
    $intervalType = $schedule.Interval.IntervalType.ToString()
    $intervalType = $intervalType.Substring(0, $intervalType.Length - 1) # Remove the last letter (s)
    $intervalCount = $schedule.Interval.Count

    try {
      $job = New-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzureRmSqlElasticJob -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
  }
  # Create or update a job that runs once
  else {
    try {
      $job = New-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -RunOnce
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzureRmSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzureRmSqlElasticJob -Description $jobDescription -RunOnce
      return $job
    }
  }
}
# Migrates job steps
function Setup-JobStep ($newJob, $job) {
  $defaultJobStepName = 'JobStep'
  $contentName = $newJob.Description
  $commandText = (Get-AzureSqlJobContentDefinition -ContentName $contentName).CommandText
  $targetGroupName = $newJob.TargetGroupName
  $credentialName = $newJob.CredentialName

  $output = $newJob.Output

  if ($output -ne $null) {
    $outputServerName = $output.TargetDescription.ServerName
    $outputDatabaseName = $output.TargetDescription.DatabaseName
    $outputCredentialName = $output.CredentialName
    $outputSchemaName = $output.SchemaName
    $outputTableName = $output.TableName
    $outputDatabase = Get-AzureRmSqlDatabase -ResourceGroupName $job.ResourceGroupName -ServerName $outputServerName -Databasename $outputDatabaseName

    try {
      $jobStep = $job | Add-AzureRmSqlElasticJobStep -Name $defaultJobStepName `
        -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzureRmSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzureRmSqlElasticJobStep -TargetGroupName $targetGroupName `
        -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
  }
  else {
    try {
      $jobStep = $job | Add-AzureRmSqlElasticJobStep -Name $defaultJobStepName -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzureRmSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzureRmSqlElasticJobStep -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
  }
  Log-ChildOutput ("Added step " + $jobStep.StepName + " using target group " + $jobStep.TargetGroupName + " using credential " + $jobStep.CredentialName)
  Log-ChildOutput("Command text script taken from content name " + $contentName)

  if ($jobStep.Output -ne $null) {
    Log-ChildOutput ("With output target as (" + $jobStep.Output.ServerName + "," + $jobStep.Output.DatabaseName + "," + $jobStep.Output.SchemaName + "," + $jobStep.Output.TableName + ")")
  }
}
```

ジョブ、ジョブ コンテンツ、ジョブ トリガー、およびジョブ スケジュールを新しいエラスティック ジョブ エージェントのデータベースに移行するには、エージェントを渡すことによって **Migrate-Jobs** コマンドレットを実行します。

- スケジュールが異なる複数のトリガーを持つジョブは、"\<jobName\> (\<scheduleName\>)" という名前付けスキームで複数のジョブに分けられます。
- ジョブ コンテンツは、JobStep という名前の既定のジョブ ステップと関連するコマンド テキストを追加することでジョブに移行されます。
- ジョブを有効にする前に検証できるように、既定ではジョブは無効になっています。

```powershell
Migrate-Jobs $agent
```

サンプル出力:
```powershell
--------------------- Migrating jobs and job steps ---------------------
Job job1
  - Repeats once
  - Added step JobStep using target group cc2 using credential cred1
  - Command text script taken from content name SampleContext
Job job2
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
  - With output target as (s1,db1,dbo,sampleTable)
Job job3 (repeat every 10 min)
  - Schedule with start time 05/16/2018 22:05:28 and end time at 12/31/9999 11:59:59
  - Repeats every PT10M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job3 (repeat every 5 min)
  - Schedule with start time 05/16/2018 22:05:31 and end time at 12/31/9999 11:59:59
  - Repeats every PT5M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job4
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
```



## <a name="migration-complete"></a>移行の完了

これで、ジョブの資格情報、ターゲット、ジョブ トリガー、ジョブ スケジュール、ジョブ コンテンツ、およびジョブがすべて*ジョブ データベース*に移行されました。

すべてが正しく移行されたことを確認するには、次のスクリプトを使用します。

```powershell
$creds = $agent | Get-AzureRmSqlElasticJobCredential
$targetGroups = $agent | Get-AzureRmSqlElasticJobTargetGroup
$jobs = $agent | Get-AzureRmSqlElasticJob
$steps = $jobs | Get-AzureRmSqlElasticJobStep
```

ジョブが正しく実行されていることをテストするには、ジョブを開始します。

```powershell
$jobs | Start-AzureRmSqlElasticJob
```

スケジュールに従って実行されていたジョブを有効にして、バックグラウンドで実行できるようにしてください。

```powershell
$jobs | Set-AzureRmSqlElasticJob -Enable
```

## <a name="next-steps"></a>次の手順

- [PowerShell を使用したエラスティック ジョブの作成と管理](elastic-jobs-powershell.md)
- [Transact-SQL を使用したエラスティック ジョブの作成と管理](elastic-jobs-tsql.md)