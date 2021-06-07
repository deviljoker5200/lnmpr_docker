1.supervisor自动启动还有问题，所以暂时只有构建好后，手动启动,每次重启容器都要手动执行
/usr/bin/supervisord -c /etc/supervisor/supervisord.conf
supervisorctl start all

2. 要在容器内部执行 storage:link才行 https://learnku.com/articles/42401