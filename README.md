unix-tools
===========

Scripts and configs for FreeBSD used for work

structure:

	rc.conf.d/	configuration scripts. Unicorn loader relies on rc.conf.d/unicorn heavily to ease multi-app setup (while it is still possible to set the whole configuration in /etc/rc.conf

	rc.d/		startup scripts
	bin/		scripts to go to /usr/local/bin or similar locations
