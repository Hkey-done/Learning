gitea创建Windows服务：

```
sc create gitea start= auto binPath= "\"C:\gitea\gitea.exe\" web --config \"C:\gitea\custom\conf\app.ini\""
```

删除：

```
sc delete gitea
```

