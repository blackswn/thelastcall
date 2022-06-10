![Build Status](https://travis-ci.org/ttsvetko/HTML5-Desktop-Notifications.svg?branch=master)
[![CircleCI](https://circleci.com/gh/ttsvetko/HTML5-Desktop-Notifications/tree/master.svg?style=svg)](https://circleci.com/gh/ttsvetko/HTML5-Desktop-Notifications/tree/master)

# HTML 5 Desktop Notification

# Support
- IE 9+
- Edge
- Firefox 22+
- Google Chrome 32+

# Notes
- [IE 9][IE 10] Does not support Promise. Polyfill required.
- [MS Edge] Notifications supported for the latest version of Edge that comes with Windows 10 Anniversary Update
- [Safari] Icon is not displayed - it uses the application icon instance of provided one
- Have to type of icons - one image format that will be displayed for Chrome/Safari/Firefox and one in .ico format, 16x16 for IE
    The library automatically will parse the icon name and will add .ico extension if it is not .ico

    The icon resource file must contain a 16x16 icon at 96 dots per inch (dpi). If an icon overlay is already applied, the existing overlay is replaced.
    Note  To view the icon overlay, the taskbar buttons must be in their default large icon mode. Small taskbar icons do not support icon overlays.
- The Notification constructor(window.Notification) is replaced with custom one in order to polyill the missing properties/methods. Each instance created with <code>new Notification('title')</code> returns the original Notification object(for Chrome/Opera/Firefox/Safari/Edge) and custom Notification object for not supported browsers:
<code>
var n = new Notification('title');
n instanceOf Notification; // false for Chrome/Safari/Opera/Edge/Firefox as created notification is instance of the browsers' native Notification object, but window.Notification is a polyfill class.
</code>

# Permissions
Notification API defines 3 permission levels for supported environments:
- <strong>default</strong> - This is equivalent to "denied", but the user has made no explicit choice thus far.
- <strong>granted</strong> - This means notifications can be displayed.
- <strong>denied</strong> - This means the user does not want notifications.

To support environments that do not implement Notification API,
this code implements an additional permission level:
- <strong>notsupported</strong> - Notification API is not supported in any format(WHATWG spec or prefixed). Notification could not be displayed. Any Notification API code execution will pass, but no notification will be displayed.

Usage:
Use the Standard API for working with this notification library:

- Notification.permission - to get the permissions for page
- Notification.requestPermission() - request permission for displaying Notifications. NOTE: Returns a Promise. For IE9 & IE10 Promise polyfill required - it is not included within this library.
- new Notification('title', options) - create new Notification. 
    More details: 
    - https://notifications.spec.whatwg.org/
    - https://developer.mozilla.org/en-US/docs/Web/API/notification

# TODO
- [ ] Home page - documentation & demo
- [x] [IE] Add support for event listeners
- [ ] [IE] Implement actions as a thumbBar buttons
- [ ] Add Support for Service Workers

# TOUNDO
- [#!/bin/bash

trap '[ "$?" -eq 0 ] || read -p "Looks like something went wrong in step ´$STEP´... Press any key to continue..."' EXIT

#Quick Hack: used to convert e.g. "C:\Program Files\Docker Toolbox" to "/c/Program Files/Docker Toolbox"
win_to_unix_path(){ 
	wd="$(pwd)"
	cd "$1"
		the_path="$(pwd)"
	cd "$wd"
	echo $the_path
}

# This is needed  to ensure that binaries provided
# by Docker Toolbox over-ride binaries provided by
# Docker for Windows when launching using the Quickstart.
export PATH="$(win_to_unix_path "${DOCKER_TOOLBOX_INSTALL_PATH}"):$PATH"
VM=${DOCKER_MACHINE_NAME-default}
DOCKER_MACHINE="${DOCKER_TOOLBOX_INSTALL_PATH}\docker-machine.exe"

STEP="Looking for vboxmanage.exe"
if [ ! -z "$VBOX_MSI_INSTALL_PATH" ]; then
  VBOXMANAGE="${VBOX_MSI_INSTALL_PATH}VBoxManage.exe"
else
  VBOXMANAGE="${VBOX_INSTALL_PATH}VBoxManage.exe"
fi

BLUE='\033[1;34m'
GREEN='\033[0;32m'
NC='\033[0m'

#clear all_proxy if not socks address
if  [[ $ALL_PROXY != socks* ]]; then
  unset ALL_PROXY
fi
if  [[ $all_proxy != socks* ]]; then
  unset all_proxy
fi

if [ ! -f "${DOCKER_MACHINE}" ]; then
  echo "Docker Machine is not installed. Please re-run the Toolbox Installer and try again."
  exit 1
fi

if [ ! -f "${VBOXMANAGE}" ]; then
  echo "VirtualBox is not installed. Please re-run the Toolbox Installer and try again."
  exit 1
fi

"${VBOXMANAGE}" list vms | grep \""${VM}"\" &> /dev/null
VM_EXISTS_CODE=$?

set -e

STEP="Checking if machine $VM exists"
if [ $VM_EXISTS_CODE -eq 1 ]; then
  "${DOCKER_MACHINE}" rm -f "${VM}" &> /dev/null || :
  rm -rf ~/.docker/machine/machines/"${VM}"
  #set proxy variables inside virtual docker machine if they exist in host environment
  if [ "${HTTP_PROXY}" ]; then
    PROXY_ENV="$PROXY_ENV --engine-env HTTP_PROXY=$HTTP_PROXY"
  fi
  if [ "${HTTPS_PROXY}" ]; then
    PROXY_ENV="$PROXY_ENV --engine-env HTTPS_PROXY=$HTTPS_PROXY"
  fi
  if [ "${NO_PROXY}" ]; then
    PROXY_ENV="$PROXY_ENV --engine-env NO_PROXY=$NO_PROXY"
  fi
  "${DOCKER_MACHINE}" create -d virtualbox $PROXY_ENV "${VM}"
fi

STEP="Checking status on $VM"
VM_STATUS="$( set +e ; "${DOCKER_MACHINE}" status "${VM}" )"
if [ "${VM_STATUS}" != "Running" ]; then
  "${DOCKER_MACHINE}" start "${VM}"
  yes | "${DOCKER_MACHINE}" regenerate-certs "${VM}"
fi

STEP="Setting env"
eval "$("${DOCKER_MACHINE}" env --shell=bash --no-proxy "${VM}" | sed -e "s/export/SETX/g" | sed -e "s/=/ /g")" &> /dev/null #for persistent Environment Variables, available in next sessions
eval "$("${DOCKER_MACHINE}" env --shell=bash --no-proxy "${VM}")" #for transient Environment Variables, available in current session

STEP="Finalize"
clear
cat << EOF


                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/

EOF
echo -e "${BLUE}docker${NC} is configured to use the ${GREEN}${VM}${NC} machine with IP ${GREEN}$("${DOCKER_MACHINE}" ip ${VM})${NC}"
echo "For help getting started, check out the docs at https://docs.docker.com"
echo
echo 
#cd #Bad: working dir should be whatever directory was invoked from rather than fixed to the Home folder

docker () {
  MSYS_NO_PATHCONV=1 docker.exe "$@"
}
export -f docker

if [ $# -eq 0 ]; then
  echo "Start interactive shell"
  exec "$BASH" --login -i
else
  echo "Start shell with command"
  exec "$BASH" -c "$*"
fi
] Fix all the shit this has lead to... 

Oh, and btw. Dont be sad Stallman.
I know this guy is rooting for you! 

https://www.youtube.com/watch?v=Vhh_GeBPOhs
