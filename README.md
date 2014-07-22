2014-07-22 更新 从mtalog中导出指定时间内remote mailfrom:  rcpt to: 已便导入反垃圾网关内
	./export_remote_user
			export 		导出
			import		导入
	策略说明：
	脚本支持多进程模式，默认进程4	可修改
	脚本支持中断后再执行 接着之前执行策略  但之前正在执行的任务会重新执行。

	导出
		1. 导出指定时间内的MTA log remote succeess 内容 (默认 90天内 )
		2. 根据配置判断该用户当天的收件人是否为可用数据（默认当天小于50个remote success为可用数据，超过则进行下一个规则）
		3. 根据配置判断超出remote次数的收件人 达到配置中to_Number 则视为可用数据 （默认当天一个发件人的收件人出现2次以上 remote success视为可用数据，超过则进行下一个规则 配置为0 则跳过该策略）
		4. 根据配置判断 以上规则不符合可用数据的 则在指定的时间内 一个发贱人的收件人出现次数 （默认出现2次以上 remote success 则视为可用数据。配置为0则跳过该策略）
		5. 判断过程中  会判断是否为本域，（可用于检测服务器被当作垃圾邮件服务器使用。），非本域内的则导出到指定日志中。默认检测mysql domain_key 中 domain_name ，如不想判断 则配置 Eyou_Domain_Name 参数 以分号;分割。
		6. 判断过程中 会跳过指定的用户 （默认策略中已跳过 mailer-daemon ）如有需求请自行添加，以分号;分割。
		7. 导出的最终数据会去重～不会出现重复数据。 数据格式为 xxx@xxx.com:::xxx@xxx.com
	导入
		1. 导入之前判断该用户的 白名单与动态白名单中是否已存在本次数据，存在则跳过，不存在则导入
		2. 默认导入到用户的动态白名单中，如需导入到白名单，请自行配置Import_type
		3. 默认导入的时间为脚本执行的时间，如需修改 请自行配置 Import_date


	导出时间 以上师大为例 90天mta数据 3分钟内导出处理完成。
	导入时间 以上师大为例 39353条可用数据，mysql 插入 时间为 30分钟。

	以上数据均在我测试机  DELL 服务器上进行的数据 8G 内存 4核至强CPU
	执行导出处理时间短，但执行导入处理时间很长，最好放入后台执行。
	


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
使用前 如果是mysql 5.1 版本 升级过来的    请将 mysql 命令  ln -s 到 /usr/bin/下，避免出现失败～
	
	./mysql_xback
			--backup|-bak	backup mysql database
					ALLNOW   NOW ALL BAK
					INCNOW	 NOW INC BAK
                        --show|-p       show backup message
                                        my.cnf / my_index.cnf /my_log.cnf
                        --repair|-r     repair mysql database
                                        my.cnf / my_index.cnf /my_log.cnf"
	--backup 实现
	全备份 会统计mysql数据库的大小（不包含binlog以及err文件） 判断备份目录所在磁盘大于  则进行备份，否则退出。
	增量备份  会统计上次备份的大小	所在磁盘 大于  则备份，否则退出。
	备份成功记录日志，备份失败则当次备份回滚。
	当全备份由于某种原因在指定的时间（配置几点备份） 超过后  之后的备份全部基于上一次的增量备份进行增量备份，并且每天的规定时间会尝试进行全量备份。 并且 全备份目录个数小于配置的删除全备份次数 则不会删除全备份，避免全备失败  到规定时间后 删除有用备份。
	当增量备份出现失败，如果是磁盘满了 那就永远不会进行备份。
	备份时如果程序bak已在运行，则新程序会退出。
	添加立即执行全备份/增量备份机制	 ALLNOW / INCNOW
	

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

