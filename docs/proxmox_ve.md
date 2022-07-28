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

若新硬碟是顯示 `/dev/sdc` 之類的訊息, 則修復指令需要更改成這樣

```shell
$ zpool replace -f rpool /dev/disk/by-id/ata-MB004000GWKGV_V1K30000-part3 /dev/sdc
```