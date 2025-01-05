---
title: "Create ,Attach and mount EBS volume on ec2 instance"
datePublished: Sun Jan 05 2025 10:13:47 GMT+0000 (Coordinated Universal Time)
cuid: cm5jggluc000909jl7ya01hqk
slug: create-attach-and-mount-ebs-volume-on-ec2-instance

---

1. Start ec2 instatance or launch a fresh one.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733293023356/17ce8bf1-f5da-4753-8bc8-5bf63f344c11.png align="center")
    
      
    2\. Run lsblk you can see the disk is attached
    
2. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733293280674/08cd4ee7-f846-4d61-a21c-22d6014a1feb.png align="center")
    
    Create Volumes for this use ebs . for this tutorial i am creatimg 3 volumes
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733293410086/fa01ed6a-b41a-4b34-b12a-ec604fdd4d97.png align="center")
    
    Attach the volume
    
3. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1733293668622/7e8617ed-8040-42b7-b867-b27cfa3be779.png align="center")
    
    swith to roo
    
4. run lvm command
    
5. to see pjysical storage run “pvs“
    
6. create a physical volume by this command : “pvcreate /dev/xvdg /dev/xvgh “
    
7. run pvs command to show the physical voume
    
8. create a volume group
    
9. vgcreate basir\_vol\_grp /dev/xvdf /dev/xvdg by running this command
    
10. run vgs to show volume group
    
11. create a logical voulune on top of it
    
12. lvcreate -L 10G -n basir\_vol basir\_vol\_grp by running this command
    
13. run pvdisplay to get all pjysical volume information
    
14. run lsblk to see logical volume is there or not
    
15. mount this volumes
    
16. if you run df -h to see all of this not mounted
    
17. create a mounting folder
    
18. mkdir /mnt/basirfolder by running this command
    
19. format logical volume
    
20. mkfs.ext4 /dev/basir\_vol\_grp/basir\_vol by running this comand
    
21. mounting volume
    
22. mount /dev/basir\_vol\_grp/basir\_vol /mnt/basirfolder by running this command
    
23. run df -h to see it is properly mounted or not
    
24. for unmounting run this command umount /mnt/basirfolder
    
25. one good question is data lost or not if i unmount
    
26. answer is mount again its back again try it at your end
    
27. for mounting afresh ebs to a linux\_ins
    
28. create a mounting folder mkdir /mnt/basiraws
    
29. formating ebs voulume by running this command : mkfs -t ext4 /dev/xvdh
    
30. mount this using mount /dev/xvdh /mnt/basiraws
    
31. want to extend logical volume
    
32. lvextend -L +5G /dev/basir\_vol\_grp/basir\_vol by running this command