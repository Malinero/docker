OVPN_HOST ?= 192.168.1.1
OVPN_IMAGE ?= kylemanna/openvpn

OVPN_DATA ?= ovpn-data-example
OVPN_NAME ?= openvpn
CLIENT_NAME ?= user

RSA_SIZE ?= -e EASYRSA_KEY_SIZE=4096
RUN_OPTIONS ?= -v ${PWD}/ovpn:/etc/openvpn --log-driver=none

create:
	docker run ${RUN_OPTIONS} --rm ${OVPN_IMAGE} ovpn_genconfig -C 'AES-256-CBC' -a 'SHA384' -u udp://${OVPN_HOST}
	docker run ${RUN_OPTIONS} --rm -it ${RSA_SIZE} ${OVPN_IMAGE} ovpn_initpki
copy:
	docker run --net=none --rm -t -i ${RUN_OPTIONS} ${OVPN_IMAGE} ovpn_copy_server_files
create_server:
	docker create --name ${OVPN_NAME} ${RUN_OPTIONS} \
		-p 1194:1194/udp \
		--cap-add=NET_ADMIN \
		${OVPN_IMAGE}
add_user:
	docker run ${RUN_OPTIONS} --rm -it ${RSA_SIZE} ${OVPN_IMAGE} easyrsa build-client-full ${CLIENT_NAME} nopass
	docker run ${RUN_OPTIONS} --rm -it  ${OVPN_IMAGE} ovpn_getclient ${CLIENT_NAME} > ${CLIENT_NAME}.ovpn

start:
	docker start ${OVPN_NAME}
stop:
	docker stop ${OVPN_NAME}
destroy: stop
	rm -rf ${PWD}/ovpn
	docker rm -v ${OVPN_NAME}

run: create_server start
