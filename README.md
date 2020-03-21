<h1>Preparation</h1>

1. Able to write Yaml Files
2. Able to use Vim Editor
3. Able to use Bash commands

ShortCuts to Setup Before Start:
```
alias k=kubectl
alias kaf="k apply -f" 
```
Setup Vim to do Yaml definition properly

```vim ~/.vimrc```

```$xslt
set nu
set ic
set expandtab
set shiftwidth=2
set tabstop=2
```

Set context appropriately 
```$xslt
k config set-context <context-name> --namespace=<namespacename>
```

Prefer to kill the objects forcefully
```$xslt
kubectl delete pod nginx --grace-period=0 --force
```







