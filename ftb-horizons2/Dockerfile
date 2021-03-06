FROM politician/java:8
MAINTAINER David Zarlengo <david@zarlengo.io>

ENV MINECRAFT_SERVER_JAR_URL=https://s3.amazonaws.com/Minecraft.Download/versions/1.7.10/minecraft_server.1.7.10.jar \
	MINECRAFT_LAUNCH_JAR_URL=https://libraries.minecraft.net/net/minecraft/launchwrapper/1.11/launchwrapper-1.11.jar \
	FTB_HORIZONS2_URL=http://ftb.cursecdn.com/FTB2/modpacks/FTBHorizonsII/1_0_2/FTBHorizonsIIServer.zip
	
# add our user and group first to make sure their IDs get assigned consistently, regardless of whatever dependencies get added
RUN		addgroup -S ftb \
 &&		adduser -D -H -G ftb ftb

# grab gosu for easy step-down from root
RUN		apk add --update curl \
 &&		curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.4/gosu-amd64" \
 &&		chmod +x /usr/local/bin/gosu

# install minecraft server packages
RUN		cd /tmp \
 &&		echo '1: fetching $MINECRAFT_SERVER_JAR_URL' \
 &&		curl -jkSL -o minecraft_server.1.7.10.jar $MINECRAFT_SERVER_JAR_URL \
 &&		echo '2: fetching $MINECRAFT_LAUNCH_JAR_URL' \
 &&		curl -jkSL -o launchwrapper-1.11.jar $MINECRAFT_LAUNCH_JAR_URL \
 &&		echo '3: fetching $FTB_HORIZONS2_URL' \
 &&		curl -jkSL -o ftb_horizons2.zip $FTB_HORIZONS2_URL \
 &&		echo '4: updating filesystem' \
 &&		mkdir -p /opt/minecraft /var/lib/minecraft | true \
 &&		unzip ftb_horizons2.zip -d /opt/minecraft \
 &&		mv minecraft_server.1.7.10.jar /opt/minecraft \
 &&		mv launchwrapper-1.11.jar /opt/minecraft/libraries/net/minecraft/launchwrapper/1.11 \
 &&		echo 'eula=true' > /opt/minecraft/eula.txt \
 &&		chown -R ftb:ftb /opt/minecraft /var/lib/minecraft \
 &&		echo '5: cleaning up' \
 &&		rm		/opt/minecraft/FTBInstall.bat \
 				/opt/minecraft/FTBInstall.sh \
 				/opt/minecraft/ServerStart.bat \
 				/opt/minecraft/ServerStart.sh \
 &&		rm -rf	/tmp/*

COPY root/ /

# add ae2 to the modpack - this'll work, right?
RUN curl -jkSL -o /opt/minecraft/mods/appliedenergistics2-rv2-stable-10.jar "http://ae-mod.info/builds/appliedenergistics2-rv2-stable-10.jar"

# NEI is broken. You have to open inventory in a singleplayer game before joining a multiplayer game.
# The bug has apparently existed for several years, and people continue to rediscover it.
# There is no fix.  Why the guy doesn't just catch the fucking exception is beyond me.  Then again,
# my logs are full of NPEs and IOOBs, so maybe this is just par for the course.

ENV MINECRAFT_HOME /opt/minecraft
ENV MINECRAFT_OPTS -server -Xms512m -Xmx2048M -d64 -XX:+CMSIncrementalPacing -XX:+CMSClassUnloadingEnabled -XX:ParallelGCThreads=2 -XX:MinHeapFreeRatio=5 -XX:MaxHeapFreeRatio=10
ENV WORLD_DIR /var/lib/minecraft
ENV MINECRAFT_STARTUP_JAR FTBServer-1.7.10-1448.jar

EXPOSE 25565

VOLUME ["/opt/minecraft", "/var/lib/minecraft"]

ENTRYPOINT ["sh"]

CMD ["/usr/local/bin/minecraft"]


