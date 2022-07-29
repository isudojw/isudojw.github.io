# Proxmox VE

## 更換 zfs pool 故障硬碟

透過 `zpool status -v` 確定出問題的 `pool名稱`  與 `硬碟ID`, 將錯誤的硬碟抽出後插入新的硬碟, 切記別拔錯了

```shell
$ zpool status -v
pool: rpool
state: DEGRADED
scan: 
config:
      NAME                                  STATE     READ WRITE CKSUM
      rpool                                 DEGRADED     0     0     0
        mirror-0                            DEGRADED     0     0     0
          ata-MB004000GWKGV_V1K30000-part3  UNAVAIL      0     0     0    too many errors
          ata-MB004000GWKGV_V1K30001-part3  ONLINE       0     0     0
        ata-MB004000GWKGV_V1K30002          ONLINE       0     0     0
```

由上面的輸出可以知道 :

- `pool_name` : rpool
- `故障硬碟` : ata-MB004000GWKGV_V1K30000-part3
- `新裝硬碟` : ata-MB004000GWKGV_V1K30002

則修復指令為

```shell
$ zpool replace -f rpool /dev/disk/by-id/ata-MB004000GWKGV_V1K30000-part3 /dev/disk/by-id/ata-MB004000GWKGV_V1K30002
```

> 基本上就是 `zpool replace -f [pool_name] [舊硬碟位置] [新硬碟位置]`

若新硬碟是顯示 `/dev/sdc` 之類的訊息, 直接套用就可以了 :

```shell
$ zpool replace -f rpool /dev/disk/by-id/ata-MB004000GWKGV_V1K30000-part3 /dev/sdc
```

修復時可以使用 `zpool status -v` 查詢進度, 不過這邊比較建議使用 web gui, 會比較方便點

## VM 強制解除鎖定

當 VM 無法透過 `qm unlock` 指令解鎖時, 可以透過下面操作強制解除

```shell
# 切換到此資料夾
$ cd /run/lock/qemu-server/

# 移除有問題的虛擬機檔案, 這邊使用編號 101 VM 做範例
rm -rf ./lock-101.conf
```