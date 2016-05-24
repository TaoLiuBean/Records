Background
==========
    vmware fusion is a good software, but TC would not like to buy this for our
    coders. So we are suggested to use virtualbox.

Issues when using virtualbox
============================
   1. the screen size does not scale freely
      --> Devices=> Insert Guest Additions CD image... (the vm related dirver
        will pop up instruct you install) 

    2. extend the disk file (xxx.vdi)
       [1] VBoxmanage modifyhd ./Ubuntu-Dev.vdi --resize 40000 (this should be
            executed under the vm directory)
       [2] restart the vm & press F12 to enter into boot media select mode.
           Select c (CD-Rom). We set the install ISO file in the CD device.
           start and try ubuntu, then use "Gparted Partition Editor" to do
            further process.
       [3] details are in http://blog.csdn.net/napolunyishi/article/details/42239897
