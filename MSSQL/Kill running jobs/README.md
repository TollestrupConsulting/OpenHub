# Kill running jobs

UNDER CONSTRUCTION:
This script isn't ready for usage yet, but will be soon ...

# Q:

I'm testing a long running job and want to stop it after it has been running for, say, 2 hours. How can I do this automatically, i.e. without being around to manually stop it?

# A:

Create another job that runs every N minutes to monitor it, and execute msdb.dbo.sp_stop_job it if it's been running longer than the desired time.

The msdb.dbo.agent_datetime(run_date,run_time) function can be used to convert the integer run_date & run_time columns to datetime.

You could probably modify the following function (determines if job is running) to take job start time into account to determine if job has been running too long, or use job_id parameter instead of job_name if you prefer.

```sql
CREATE FUNCTION [dba].[IsJobRunning]
(
   @JobName SYSNAME
)
RETURNS BIT
AS
BEGIN
   
    DECLARE @job_id uniqueidentifier = (SELECT job_id FROM msdb.dbo.sysjobs WHERE name = @JobName);
    DECLARE @isRunning BIT = 0;

    IF EXISTS ( SELECT *
                FROM msdb.dbo.sysjobs_view 
                INNER JOIN msdb.dbo.sysjobactivity  ON 
                    sysjobs_view.job_id = sysjobactivity.job_id
                INNER JOIN msdb.dbo.syssessions ON
                    syssessions.session_id = sysjobactivity.session_id
                INNER JOIN (SELECT MAX(agent_start_date) AS max_agent_start_date
                            FROM msdb.dbo.syssessions
                            ) sess_max ON 
                    syssessions.agent_start_date = sess_max.max_agent_start_date
                WHERE 
                    sysjobs_view.job_id = @job_id AND 
                    sysjobactivity.run_requested_date IS NOT NULL AND 
                    sysjobactivity.stop_execution_date IS NULL 
              )
        BEGIN
            SET @isRunning = 1;
        END

    RETURN @isRunning;

END
```

It's important to join to syssessions:

See blog post: [A T-SQL query to get current job activity](https://sqlstudies.com/2013/09/05/a-t-sql-query-to-get-current-job-activity/)
