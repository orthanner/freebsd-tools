#!/bin/sh
#
# jboss startup script.
#
# $FreeBSD: ports/java/jboss5/files/jboss5.sh.in,v 1.4 2012/01/14 08:55:51 dougb Exp $
#

# PROVIDE: jboss
# REQUIRE: NETWORKING SERVERS

# Add the following lines to /etc/rc.conf to enable jboss:
# jboss_enable (bool):      Set to "YES" to enable jboss
# jboss_jvm_opts (str):     Extra JVM flags.
# jboss_args (str):         Optional arguments to JBoss
# jboss_logging (str)       JBoss log output. A pipe command may be used.
#

. /etc/rc.subr

jboss_user="jboss"
jboss_logdir="/var/log/jboss"

name="jboss"
rcvar=jboss_enable

load_rc_config $name

jboss_enable="${jboss_enable:-NO}"
jboss_jvm_opts="${jboss_jvm_opts:-'-server \
	-Xms128m -Xmx512m -XX:MaxPermSize=256m \
	-Dorg.jboss.resolver.warning=true \
	-Dsun.rmi.dgc.client.gcInterval=3600000 \
	-Dsun.rmi.dgc.server.gcInterval=3600000'}"

start_cmd="jboss_start"
stop_cmd="jboss_stop"
status_cmd="jboss_status"
extra_commands="status"
pidfile="/var/run/jboss/jboss.pid"

JBOSS_HOME="/opt/jboss-as"
JBOSS_DEPLOY="/opt/jboss-as/standalone/deployments"
JBOSS_MAIN="org.jboss.Main"
JAVA_OPTS="${jboss_jvm_opts} \
  -Djboss.server.base.dir=${JBOSS_DEPLOY} \
  -Djboss.server.base.url=file://${JBOSS_DEPLOY} \
  -Djava.endorsed.dirs=${JBOSS_HOME}/lib/endorsed \
  -classpath ${JBOSS_HOME}/bin/run.jar ${JBOSS_MAIN}"

jboss_start ()
{
	if [ ! -d "${jboss_logdir}" ]
	then
		mkdir -p ${jboss_logdir}
		chown ${jboss_user} ${jboss_logdir}
	fi

	echo "Starting jboss."
	daemon -u ${jboss_user} -p $pidfile ${JBOSS_HOME}/bin/standalone.sh ${jboss_args} -b 0.0.0.0 -bm 0.0.0.0
}

jboss_stop ()
{
	# Subvert the check_pid_file procname check.
	if [ -f ${pidfile} ]
	then
		read rc_pid junk < $pidfile
		if [ ! -z "${rc_pid}" ]
		then
			procname=`ps -o ucomm= ${rc_pid}`
		fi
	fi

#	rc_pid=$(check_pidfile $pidfile *$procname*)

	if [ -z "${rc_pid}" ]
	then
		[ -n "${rc_fast}" ] && return 0
		if [ -n "${pidfile}" ]
		then
			echo "${name} not running? (check ${pidfile})."
		else
			echo "${name} not running?"
		fi
		return 1
	fi

	echo "Stopping ${name}."
	kill ${rc_pid} 2> /dev/null
	jboss_wait_max_for_pid 30 ${rc_pid}
	kill -KILL ${rc_pid} 2> /dev/null && echo "Killed."
	rm -f ${pidfile}
}

jboss_status ()
{
	# Subvert the check_pid_file procname check.
	if [ -f ${pidfile} ]
	then
		read rc_pid junk < $pidfile
		if [ ! -z "${rc_pid}" ]
		then
			procname=`ps -o ucomm= ${rc_pid}`
		fi
	fi

#	rc_pid=$(check_pidfile $pidfile *${procname}*)

	
	if [ -z "${rc_pid}" ]
	then
		if [ -n "${pidfile}" ]
		then
			echo "${name} not running (check ${pidfile})."
		else
			echo "${name} not running"
		fi
		return 1
	fi
	
	echo "${name} is running"
	return 0
}

jboss_wait_max_for_pid ()
{
	_timeout=$1
	shift
	_pid=$1
	_prefix=
	while [ $_timeout -gt 0 ]
	do
		echo -n ${_prefix:-"Waiting (max $_timeout secs) for PIDS: "}$_pid
		_prefix=", "
		sleep 2
		kill -0 $_pid 2> /dev/null || break
		_timeout=$(($_timeout-2))
	done
	if [ -n "$_prefix" ]; then
		echo "."
	fi
}

run_rc_command "$1"
