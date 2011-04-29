# Luggage recipe to create a package that creates a local user
# (C) 2011 Anders Bruvik
# a.s.bruvik@usit.uio.no
# 

include /usr/local/share/luggage/luggage.make

# Edit the following parameters:
PACKAGE_VERSION=1.0.0
REVERSE_DOMAIN=no.uio
SHORTNAME=myadminuser
REALNAME="Evil system administrator"
UID=400
GID=20 #20 is "staff"
SHELL=/bin/bash
PICTURE="/Library/User\ Pictures/Fun/Robot.tif"
GENUID=B36EE516-5E79-44C1-B3DC-5C6623FFDE0B
USER_HOME=/var/${SHORTNAME}

# variables
TITLE=${SHORTNAME}
TEMP_NODE=${WORK_D}/private/var/db/dslocal/nodes/Default/
USERPATH=/Local/Target/Users/${SHORTNAME}
PASSWORDHASH=password_hash
POSTFLIGHT=${RESOURCE_D}/postflight
DSCL=/usr/bin/dscl
PAYLOAD=create_user \
	create_postinstall_script

# Create our user in a temp. directory node, using dscl: 
create_user: l_root
	@mkdir -p ${TEMP_NODE}
	@chown root:wheel ${TEMP_NODE}
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH}
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} realname ${REALNAME}
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} gid ${GID}
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} uid ${UID}
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} NFSHomeDirectory ${USER_HOME}
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} generateduid ${GENUID}
	${DSCL} -f ${TEMP_NODE} localonly -merge ${USERPATH} authentication_authority ";ShadowHash;"
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} passwd "*"
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} picture "${PICTURE}"
	${DSCL} -f ${TEMP_NODE} localonly -create ${USERPATH} shell ${SHELL}
	@mkdir -p ${WORK_D}/private/var/db/shadow/hash
	@chmod 700 ${WORK_D}/private/var/db/shadow/hash
	@${CP} ${PASSWORDHASH} ${WORK_D}/private/var/db/shadow/hash/${GENUID}
	@chmod 600 ${WORK_D}/private/var/db/shadow/hash/${GENUID}

# add user to admin group has to be done post install. So let us
# create our postinstallscript:
create_postinstall_script: resourcedir
	@echo "#!/bin/bash">${POSTFLIGHT}
	# hide user from loginwindow
	@echo "/usr/bin/defaults write \$$3/Library/Preferences/com.apple.loginwindow HiddenUsersList -array-add ${SHORTNAME}" >> ${POSTFLIGHT}
	# add user to admin group
	@echo "dscl -f \$$3/var/db/dslocal/nodes/Default localonly -merge /Local/Target/Groups/admin GroupMembership ${SHORTNAME}">>${POSTFLIGHT}
	@echo "dscl -f \$$3/var/db/dslocal/nodes/Default localonly -merge /Local/Target/Groups/admin GroupMembers ${GENUID}">>${POSTFLIGHT}
	# create home directory for user
	@echo "mkdir -p \$$3/${USER_HOME}"
	@chmod 755 ${POSTFLIGHT}


#	$serviceCmd "$dsNode" $suffix -merge $adminPath $groupKey "${shortname}"
# adminPath="${databasePath}Groups/admin"