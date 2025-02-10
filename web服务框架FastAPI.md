使用:
```python

db.ozon_category.find({'二级分类': '厨房'})
if __name__ == "__main__":
    uvicorn.run("app:app", reload=True, port=5050, host="0.0.0.0")  //在python中启动 host="0.0.0.0"允许所有网络连接

终端用 nohup uvicorn app:app --host 0.0.0.0 --port 5055 > uvicorn.log 2>&1 &
在后台运行
#关闭后台运行
htop, 找到uvicorn的进程F9关闭

#传文件到服务器
scp -r -i /home/dav/Github/ssh/cloudocr/ligpt/ssh-key-2023-11-28.key /home/dav/ozon/ozon/ozon ubuntu@193.122.147.249:/home/ubuntu/

#甲骨文云提权
chmod 600 ssh-key-2023-05-28.key

#恢复mongodb数据库
mongorestore --nsInclude=ozon.ozon_products ~/ozon/
#备份mongodb数据库
mongodump --db ozon --collection ozon_products --out  ~/ozon/ozon/
```


