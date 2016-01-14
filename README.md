#!/bin/bash
DIALOG_CANCEL=1
DIALOG_ESC=255
HEIGHT=0
WIDTH=0
display_result() {                                 
dialog --title "$1" \
--no-collapse \
--msgbox "$result" 0 0
}
while true; do
exec 3>&1
selection=$(dialog \
--backtitle "System Information" \                  # create a dialog box that will be present to the user 
--title "Current directory" \                       # containing optional tasks that the utility can perform 
--clear \
--cancel-label "Exit" \
--menu "Please select:" $HEIGHT $WIDTH 4 \
"1" "Display System Information" \
"2" "Display Disk Space" \
"3" "Display Home Space Utilization" \
2>&1 1>&3)
exit_status=$?
exec 3>&-
case $exit_status in
$DIALOG_CANCEL)
clear
echo "Program terminated."
exit
;;
$DIALOG_ESC)
clear
echo "Program aborted." >&2
exit 1
;;
esac
case $selection in
0 )
clear
echo "Program terminated."                            #if nothing from the utility is selected quit the program.
;;
1 )
result=$(echo "Hostname: $HOSTNAME"; uptime)          # if "Display System Information" is entered then provide the user with the name
display_result "System Information"                   # of the system
;;
2 )
result=$(df -h)                                       # display the file systems disk space usage that is readable
display_result "Disk Space"                
;;
3 )
if [[ $(id -u) -eq 0 ]]; then
result=$(du -sh /home/* 2> /dev/null)
display_result "Home Space Utilization (All Users)" 
else                                                   # if the "Display Home space utilzation" is selected then provide the user with
result=$(du -sh $HOME 2> /dev/null)                    # an estimated file space in the home directory              
display_result "Home Space Utilization ($USER)"
fi
;;
esac
done


