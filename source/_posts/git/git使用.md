---
Title: git使用	
---

```
git config --global credential.helper store  # 避免每次pull/commit..的时候需要输入账号密码
# 出现这种提示信息时info: please complete authentication in your browser...
git config --system --unset credential.helper # Git Credential Manager 
#配置全局账号密码
git config --global user.password "123456"
git config --global user.name "123456"
```

