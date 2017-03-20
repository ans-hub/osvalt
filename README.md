# Osvalt (Old-school viewer: apache log tool)

## Description:

Every time when I came back to one of the servers, where my sites is placed, I had imagined command like this:
~~~~
$ show_me_logs_of_siteN_for_period_and_only_some_fields
~~~~
or this:
~~~~
$ show_me_logs_of_siteN_for_2_hours_ago_and_who_are_there_generate_404_error
~~~~
Of course, my next steps was is doing routines by `find`, `awk`, `sed` and other, then solving the problems like comparing dates Jul and Aug and others. And once upon a time I wrote the 
bash script that perform getting apache access logs data for any period in easy way.

As result my dream embodied in command like this:
~~~~
$ osvalt "2 hours ago" "now" fip uniq
~~~~
or (if I want to monitoring some activity for periods) such command has been called by cron:
~~~~
$ osvalt "10 minutes ago" "now" fcode fip furl uniq | grep " 404 " | mail --subject="Check" mon@ans.sh
~~~~

## File descriptions:

- `osvalt`      - main script that provides data filtering

- `dasp`        - helper script that provides getting data from apache logs for any period

- `completion`  - script with autocomplete function

## Install script:
~~~~
git clone https://github.com/ans-hub/osvalt.git
~~~~

### Be sure that all works fine:
~~~~
$ cd osvalt_dir/test
$ ./osvalt_test_main
~~~~
### Make script executable:
~~~~
$ chmod +x osvalt
~~~~

### Make symbolic link to script
~~~~ 
$ sudo ln -s ~/folder_with_osvalt/osvalt /usr/local/bin/osvalt
~~~~
If you haven't access to /usr/local/bin/ or any directory from PATH, you may create any dir, for example, `~/bin/`, and add this dir to path via `.bashrc`:
~~~~
$ PATH="${PATH}:~/bin/"
~~~~
*Note: in next steps all code writed without "./", as if you have performed command above.*
### Set alias to osvalt with options:
~~~~
$ alias osvalt="osvalt -s path/to/logs -o 2"
~~~~
*Note: if modification time of your files in `path/to/logs` directory is not corresponds to dates in files, for example, this is old archive, then you must remove -o option.

### Read built-in help for command usage:
~~~~
$ osvalt -h
~~~~
## Usage examples:
~~~~
# Show all logs for period
$ osvalt "yesterday" "now" vall

# Show ip statistic for 2 days
$ osvalt "2 days ago" "today" fip uniq

# Show traffic by codes and by hours
$ osvalt "2017-01-02 10:00:00" "now" tsize_c treq_h

# Who generate 404 error
$ osvalt "12:00" "13:00" fcode fip furl uniq | grep " 404 "

# Show pages with 303 and 503 code:
$ osvalt "2 days ago" "now" fcode fip freq | grep "^303\|503"

# Show refferers without self domain
$ osvalt "12:00" "13:00" fref uniq | grep -v "ans.sh" 
~~~~
## Additional options to osvalt:

You may use an additional options to osvalt, such as:
  - custom months (if month is present in logs in the another language, for example)
  - searching in logs only by file modification time
  - using temp file instead of var if you work with big data
And some another useful options.

## Install completitions in two ways

To load completions over .bashrc file, put this lines to .bashrc file on your home dir:
~~~~
$ source path_to_osvalt_dir/completion
~~~~
or if you have access to this path 
~~~~
$ cp path_to_osvalt_dir/completion /etc/bash_completion.d/
~~~~
## Perfomance issues:
To increase perfomance, you may:
- use option -o for getting lines only from files from defined dates +- offset
- use option -t for storing temp data in the temp file instead of variable
- use option -n to take start_date info from file
## Known issues:
Current version not working with time zones. If your logs written by timezone offset, you may use option -o for searching in files by date +- days offset.
