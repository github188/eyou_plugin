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
	--backup 实现
	全备份 会统计mysql数据库的大小（不包含binlog以及err文件） 判断备份目录所在磁盘大于  则进行备份，否则退出。
	增量备份  会统计上次备份的大小	所在磁盘 大于  则备份，否则退出。
	备份成功记录日志，备份失败则当次备份回滚。
	当全备份由于某种原因在指定的时间（配置几点备份） 超过后  之后的备份全部基于上一次的增量备份进行增量备份，并且每天的规定时间会尝试进行全量备份。 并且 全备份目录个数小于配置的删除全备份次数 则不会删除全备份，避免全备失败  到规定时间后 删除有用备份。
	当增量备份出现失败，如果是磁盘满了 那就永远不会进行备份。
	备份时如果程序bak已在运行，则新程序会退出。
	

	--show 实现
		可加参数 配置文件名称  如 my.cnf my_index.cnf my_log.cnf
	具体的备份时间   星期    备份的目录    主目录是谁   会打印出来。

	--repair 实现
		可加参数 配置文件名称  如 my.cnf my_index.cnf my_log.cnf
	基于配置文件 如my.cnf 的恢复，每次恢复只支持一个数据库实例的恢复，恢复的repair_id 只显示全备的id   恢复过程中会将基于此次全备份的所有增量备份进行恢复。
	恢复之前 会进行磁盘检测，如果数据库所在目录空间大小不足，则会退出 并且进行提示。
	恢复时如果程序repair已在运行，则新程序会退出。

	基于crontab 计划任务进行  挂载crontab  使用以下即可。
	* * * * * ./mysql_xback -bak

