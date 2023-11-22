# Ubuntu（WSL）

## pip

### 如果没有pip

```
sudo apt-get update
sudo apt-get upgrade  
```

```
sudo apt install python3-pip
```

### pip更新

```
pip3 install --upgrade pip
```

### requirements.txt

```
# 生成requirements.txt
pip freeze > requirements.txt

# 安装requirements.txt中的库
pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

```

### 全局换源

```
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

