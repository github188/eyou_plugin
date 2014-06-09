检测eyou 业务mysql 数据库逻辑关系判断修复主键冲突
	./rebuild_mysql_all
		-u -c	check user
		-u -r 	rebuild	user sql导入文件
		-u -h	help user

		-s -c	check system
		-s -r	rebuild system
		-s -h	help system
	
		-a -c	check all



备份mysql 基于innobackupex 的备份。 查看，恢复 等。
	
	./mysql_xback
			--backup|-bak	backup mysql database
			--show|-p	show backup message
			--repair|-r	repair mysql database
