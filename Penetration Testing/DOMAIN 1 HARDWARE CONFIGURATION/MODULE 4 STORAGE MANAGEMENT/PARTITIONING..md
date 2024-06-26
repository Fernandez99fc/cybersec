OBJECTIVES
--
UNDERSTAND THE MBR AND GPT PARTITIONING
UNDERSTAND PARTITIONING WITH THE FDISK COMMAND 
USE THE GDISK COMMAND TO CREATE PARTITIONS.
MBR
--
THE MBR(MASTER BOOT RECORD) PARTITIONING WAS DESIGNED TO HOLD FOUR PARTITION TABLE ENTRIES.
-LATER THIS LIMITATION WAS BYPASSED BY ALLOWING EXTENDED PARTITIONS.
-HOWEVER YOU CAN STILL HAVE 4 PRIMARY PARTITIONS.

A DISK USING MBR IS LIMITED TO THE MAXIMUM SIZE OF 2.2 TB DUE TO 32 BIT ADDRESSING. MBR PARTITIONING ARE CLOSELY RELATED TO BIOS AND LEGACY BOOT

GPT
--
GUID PARTITION TABLE (GPT) WAS CREATED TO OVERCOME THE LIMITATION OF MBR(BIOS).
-GPT IS ONE PARTITIONING THAT EXTENDS ACROSS A DISC.
-PARTITION COMPONENTS ARE LOGICAL BLOCK ADDRESSES(LBAs)
GPT USES 64BITS LOGICAL ADDRESSING, WHICH ALLOWS DRIVES OF UP TO 8 ZETTABYTES IN SIZE.
GPT PARTITIONS ARE CLOSELY RELATED WITH UEFI- THE EFI SYSTEM PARTITION (ESP) NEEDED TO BOOT IS A GPT PARTITION.  

FDISK COMMAND
--
WHEN WE THINK ABOUT FDISK, WE SHOULD THINK ABOUT MBR,BIOS/LEGACY BOOT.
CHECKING OUR DISK DEVICES ON OUR SYSTEM BY DOING THE LS COMMAND ON /DEV/SD*:
LS /DEV/SD*- TO LIST DISK DEVICES STARTING WITH SD..

/DEV/SDA IS A DISK. /DEV/SDA1-5 ARE DISK PARTITIONS.

FDISK -l TO LIST INFORMATION ABOUT THE DISK DEVICE- THE STORAGE ETC.
TO SPECIFY THE DISK DEVICE, WE CAN USE FDISK -l /DEV/SDA- TO LIST INFORMATION ABOUT THE DISK DEVICE
TO MANAGE THE SDA DISK - WE USE THE COMMAND FDSIK /DEV/SDA

GDISK COMMAND
--
WHEN YOU SEE GDISK, THINK ABOUT UEFI/EFI AND LARGE DRIVES BIGGER THAN 2.2 TB. USING FDISK ALLOWS YOU TO GO UP TO 2.2TB IN SIZE BUT YOU CAN GO UP TO 8 ZETTABYTE USING GPT PARTITION.
GDISK -l /DEV/SDA- LIST SDA INFORMATION
GDISK /DEV/SDA- MANAGE SDA DISK AND PARTITIONS.