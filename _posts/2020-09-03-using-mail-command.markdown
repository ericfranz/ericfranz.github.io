---
layout: post
title: "Using the mail command from OSC"
date: 2020-09-03T10:55:00-05:00
---

The simplest approach to sending mail from OSC clusters is to use the mail command. This is the command that Slurm uses to send email to users about their jobs. 

```
echo "Example email from OSC with attachment." | mail -s "Now with a subject" -a Demo-OnDemand-App-Dev.pdf  johrstrom@osc.edu efranz@osc.edu
```

- stdin was used for the body of the email
- `-s` is used to specify the subject
- `-a` is used to specify the attachment
- then what follows is a space delimited series of email addresses to send the email to

The -E flag is useful in crons: it tells mail to refuse to send the email if the message body is empty. For example, the below cron job spams my email with stderr of the job. But the mail command does nothing because stdout is empty. 

```
efranz@owens-login03:~$ crontab -l
* * * * * echo "You should not receive this email." >&2 | mail -E -s "Now with a subject" -a Demo-OnDemand-App-Dev.pdf  johrstrom@osc.edu efranz@osc.edu
```

So if you create a report script called report.R that you want to run every day at 130 you would have report.R generate that report and print to stdout if everything goes well. So the last statement of the script would output the email message body. If an error occurred you would want to ensure report.R only wrote output to stderr - no stdout - so that no mail was sent.

30 13 * * * module load R/3.6.3-gnu9.1; R /fs/ess/PZS0714/reports/report.R | mail -E -s "Daily Report" -a /fs/ess/PZS0714/reports/report.pdf johrstrom@osc.edu efranz@osc.edu

You can create an alias for a list of email addresses in a .mailrc file if you wanted to manage the list that way, though I have not tested this with cron. “Personal and system wide distribution lists” on https://www.commandlinux.com/man-page/man1/Mail.1.html explains in more detail. For example:

```
# .mailrc
alias reportusers johrstrom@osc.edu efranz@osc.edu

# mail the report
echo "The report is attached" | mail -s "Daily Report" -a /fs/ess/PZS0714/reports/report.pdf reportusers
```

Of course, email is inherently insecure. Use GPG to encrypt sensitive data sent by email.
