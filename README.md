# backup 

```bash
#! /bin/bash

URL="${1:-help}"
MASTER_PWD="${2:-admin}"
NAME="${3:-db_name}"
FORMAT="${4:-zip}"
LOCATION="${5:-./}"


function backup_help(){
    echo "Usage: backup.sh [options...]
arg1, URL odoo server
arg2, Master password
arg3, Database name
arg4, Backup format  zip or dump
arg5, Location to save the backup
"
    echo "How to use?
backup.sh http://localhost:8069 admin demo zip ./
"    
}
function backup_request(){
    backup_location="$3-$(date +"%Y%m%d-%H%M%S").$4"
    curl "$1/web/database/backup" \
        -X POST \
        --header "Content-Type: application/x-www-form-urlencoded" \
        --data "master_pwd=$2&name=$3&backup_format=$4" \
        --output "$backup_location"
}

case $1 in
    http*)
        backup_request "$URL" "$MASTER_PWD" "$NAME" "$FORMAT" "$LOCATION"
        ;;

    *)
        backup_help
        ;;
esac    
```

# restore

```bash
curl -F 'master_pwd=admin_passwd' -F backup_file=@/path/to/file.zip -F 'copy=true' -F 'name=db_name' http://localhost:8069/web/database/restore
```
