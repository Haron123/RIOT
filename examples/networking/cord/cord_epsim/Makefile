# name of your application
APPLICATION = cord_epsim

# If no BOARD is found in the environment, use this default:
BOARD = adafruit-feather-nrf52840-sense

# This has to be the absolute path to the RIOT base directory:
RIOTBASE ?= $(CURDIR)/../../../..

# Enable GNRC networking
USEMODULE += netdev_default
USEMODULE += auto_init_gnrc_netif
USEMODULE += gnrc_ipv6_default

# Run the simple CoRE resource directory
USEMODULE += cord_epsim

# We also use the xtimer for periodic RD entry updates in this example
USEMODULE += xtimer

# Comment this out to disable code in RIOT that does safety checking
# which is not needed in a production environment but helps in the
# development process:
DEVELHELP ?= 1

# The RD server's address must be defined by the build environment by setting
# the RD_ADDR environment variable. Per default, this value is set to the
# loopback address for enabling the build tests to successfully build this
# example.
RD_ADDR ?= \"[2001:67c:254:b0b2:affe:3000:0:1]\"
CFLAGS += -DRD_ADDR=$(RD_ADDR)

include $(RIOTBASE)/Makefile.include

# For debugging and demonstration purposes, we limit the lifetime to just one
# minute
RD_LT ?= 60

# Set CONFIG_CORD_LT only if not being set via Kconfig
ifndef CONFIG_CORD_LT
CFLAGS += -DCONFIG_CORD_LT=$(RD_LT)
endif

term_custom:
	$(RIOTBASE)/dist/tools/pyterm/pyterm -p "/dev/$(PORT)" -b "115200" -ln "/tmp/pyterm-haron"

burn:
	# Create UF2
	$(RIOTBASE)/dist/tools/uf2/uf2conv.py -f 0xADA52840 ./bin/adafruit-feather-nrf52840-sense/cord_epsim.hex --base 0x26000 -o ./programm.uf2

	# Create UF2 as ZIP
	adafruit-nrfutil dfu genpkg --dev-type 0x0052 --application ./bin/adafruit-feather-nrf52840-sense/cord_epsim.hex cord_epsim.zip

	# Upload
	adafruit-nrfutil dfu serial --package cord_epsim.zip -p /dev/$(PORT) -b 1152000

node1:
	@echo "Node 1 selected"
	$(MAKE) clean
	$(MAKE) -j12 all CFLAGS+=" -DMYID=1 "
	$(MAKE) burn PORT=ttyACM0
	$(MAKE) term_custom PORT=ttyACM0