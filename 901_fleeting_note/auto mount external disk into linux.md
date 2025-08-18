


```

# sudo blkid
# 看 uuid

/dev/sda2: LABEL="TOSHIBA-2" UUID="5A35-27B3" BLOCK_SIZE="512" TYPE="exfat" PARTUUID="92bd1f2e-02"
/dev/sda1: LABEL_FATBOOT="FAT" LABEL="FAT" UUID="3B0A-1BF4" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="92bd1f2e-01"


編輯這個檔案
# sudo vi /etc/fstab



```

add below line
之間相隔是 tab or use vim 來 `w` to go to
type 一樣用上來來看
![[IMG-auto mount external disk into linux-20241003104934155.png]]

ref: [(602) How to Auto Mount Drives in Linux on Boot - YouTube](https://www.youtube.com/watch?v=LkwZZIsY9uE&t=7s)
[Allowing external drives to be recognized in Jellyfin Ubuntu (Beat Over the Head Edition) : r/jellyfin](https://www.reddit.com/r/jellyfin/comments/qderwx/allowing_external_drives_to_be_recognized_in/)
