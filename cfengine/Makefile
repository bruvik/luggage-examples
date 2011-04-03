include /usr/local/share/luggage/luggage.make

TITLE=usit-cfengine
REVERSE_DOMAIN=no.uio.usit
PACKAGE_VERSION=1.0.0
PACKAGE_NAME=usit-cfengine

# we temp directory to build everything in
BUILD_D=${SCRATCH_D}/build

#sources
BD_SOURCE=db-4.8.30.tar
CFENGINE_SOURCE=cfengine-3.1.4.tar
PCRE_SOURCE=pcre-8.12.tar

#source dirs:
PCRE_SOURCE_D=$(shell /usr/bin/basename ${PCRE_SOURCE} .tar)
BD_SOURCE_D=$(shell /usr/bin/basename ${BD_SOURCE} .tar)
CFENGINE_SOURCE_D=$(shell /usr/bin/basename ${CFENGINE_SOURCE} .tar)

# configure options
PREFIX=--prefix=/opt/cfengine/
COMPILER_FLAGS=CFLAGS="-arch i386" LDFLAGS="-arch i386"
PCRE_CONFIG_OPTIONS=${PREFIX} ${COMPILER_FLAGS} CC='gcc -m32' CXX='g++ -m32'
BD_CONFIG_OPTIONS=${PREFIX} ${COMPILER_FLAGS} 
CFENGINE_CONFIG_OPTIONS=${PREFIX} ${COMPILER_FLAGS} --with-berkeleydb=/opt/cfengine --with-pcre=/opt/cfengine 


PAYLOAD=\
	build_cfengine\
        pack-script-postflight\
	pack_var \
	pack_cfengine

# install files to /opt/cfengine?

# copy to var
# include post-install-script

# make temporary dirs for cfengine
l_opt_cfengine: scratchdir l_root
	@sudo mkdir -p ${WORK_D}/opt/cfengine
	@sudo chown -R root:wheel ${WORK_D}/opt/cfengine
	@sudo chmod -R 755 ${WORK_D}/opt/cfengine

l_var_cfengine: l_var
	@sudo mkdir -p ${WORK_D}/var/cfengine/bin
	@sudo mkdir -p ${WORK_D}/var/cfengine/ppkeys
	@sudo mkdir -p ${WORK_D}/var/cfengine/inputs

make_build_dir: l_root
	@sudo mkdir -p ${BUILD_D}
	@sudo chown -R root:wheel ${BUILD_D}
	@sudo chmod -R 755 ${BUILD_D}

# copy files to temp-dir. Maybe we should be more specific?
pack_cfengine: l_opt_cfengine
	@sudo ${CP} -r /opt/cfengine/sbin ${WORK_D}/opt/cfengine
	@sudo ${CP} -r /opt/cfengine/include ${WORK_D}/opt/cfengine
	@sudo ${CP} -r /opt/cfengine/lib ${WORK_D}/opt/cfengine

# copy failsafe to /var/cfengine/inputs
pack_var: l_var_cfengine
	@sudo ${CP} ../stage/inputs/failsafe.cf  ${WORK_D}/var/cfengine/inputs
	@sudo ${CP} ./ppkeys/*  ${WORK_D}/var/cfengine/ppkeys/
	@sudo chown -R root:wheel ${WORK_D}/var/cfengine

build_pcre: make_build_dir
	@echo "building pcre"
	sudo ${CP} ${PCRE_SOURCE} ${BUILD_D}/&& \
	cd ${BUILD_D}&& \
	/usr/bin/tar -xf ${BUILD_D}/${PCRE_SOURCE}&& \
	cd ${BUILD_D}/${PCRE_SOURCE_D}&&\
	./configure ${PCRE_CONFIG_OPTIONS}&&\
	make &&\
	sudo make install &&\
	sudo chmod -R og+rX /opt/cfengine


build_bd: make_build_dir
	@echo "building bd"
	sudo ${CP} ${BD_SOURCE} ${BUILD_D} && \
	cd ${BUILD_D} &&\
	/usr/bin/tar -xf ${BUILD_D}/${BD_SOURCE}&& \
	cd ${BUILD_D}/${BD_SOURCE_D}/build_unix &&\
	../dist/configure ${BD_CONFIG_OPTIONS} && \
	make && \
	sudo make install &&\
	sudo chmod -R og+rX /opt/cfengine

build_cfengine: build_pcre build_bd
	@echo "building cfengine"
	@echo  ${CFENGINE_CONFIG_OPTIONS}
	sudo ${CP} ${CFENGINE_SOURCE} ${BUILD_D} && \
	cd ${BUILD_D} && \
	/usr/bin/tar -xf ${BUILD_D}/${CFENGINE_SOURCE}&& \
	cd ${BUILD_D}/${CFENGINE_SOURCE_D} &&\
	./configure ${CFENGINE_CONFIG_OPTIONS} && \
	make && \
	sudo make install &&\
	sudo chmod -R og+rX /opt/cfengine

