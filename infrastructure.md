# Nexus #

- 私服地址：[http://nexus.ppc.com/nexus/](http://nexus.ppc.com/nexus/ "http://nexus.ppc.com/nexus/")
- 工程pom.xml的仓库配置
		
		<distributionManagement>
			<repository>
				<id>ppc-releases</id>
				<name>ppcredit nexus releases</name>
				<url>http://nexus.ppc.com/nexus/content/repositories/releases/</url>
			</repository>
			<snapshotRepository>
				<id>ppc-snapshot</id>
				<name>ppcredit nexus snapshot</name>
				<url>http://nexus.ppc.com/nexus/content/repositories/snapshots/</url>
			</snapshotRepository>
		</distributionManagement>		

		<repositories>
			<repository>
				<snapshots>
					<enabled>true</enabled>
				</snapshots>
				<id>ppc-snapshot-repo</id>
				<name>ppcredit snapshot repositories</name>
				<url>http://nexus.ppc.com/nexus/content/repositories/snapshots/</url>
			</repository>
			<repository>
				<releases>
					<enabled>true</enabled>
					<updatePolicy>always</updatePolicy>
					<checksumPolicy>ignore</checksumPolicy>
				</releases>
				<id>ppc-release-repo</id>
				<name>ppcredit release repositories</name>
				<url>http://nexus.ppc.com/nexus/content/repositories/releases/</url>
			</repository>
		</repositories>
- 本地Maven settings.xml配置，参照如上信息

# Jira #
- 地址：[http://jira.ppc.com/](http://jira.ppc.com/ "http://jira.ppc.com/")
- 用户名、密码：LDAP

# Git #
- 地址：[http://git.ppc.com/](http://git.ppc.com/ "http://git.ppc.com/")

# Consul & InfluxDB #
地址：参阅 [http://wiki.bs.ppcredit.com/index.php/%E5%BE%AE%E6%9C%8D%E5%8A%A1](http://wiki.bs.ppcredit.com/index.php/%E5%BE%AE%E6%9C%8D%E5%8A%A1 "Wiki")

# Apollo #
地址：[http://portal.online.net.ppcredit.com/](http://portal.online.net.ppcredit.com/ "Apollo")