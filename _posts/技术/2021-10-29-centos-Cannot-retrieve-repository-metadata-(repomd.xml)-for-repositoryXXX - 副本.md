# Cannot retrieve repository metadata (repomd.xml) for repository:XXX. Please verify its path and try again

``` 
Try this (has to be root)
```

```
yum clean all
yum check
yum erase apf
yum update ca-certificates
yum upgrade
```



