OSRM_RELEASE := $(shell node -e "console.log(require('../../package.json').osrm_release)")

ifeq ($(OSRM_RELEASE),)
$(error OSRM_RELEASE variable was not correct set)
endif

CAR_PROFILE_URL:=https://raw.githubusercontent.com/Project-OSRM/osrm-backend/$(OSRM_RELEASE)/profiles/car.lua
LIB_GUIDANCE_URL:=https://raw.githubusercontent.com/Project-OSRM/osrm-backend/$(OSRM_RELEASE)/profiles/lib/guidance.lua
PROFILE_LIB_ACCESS_URL:=https://raw.githubusercontent.com/Project-OSRM/osrm-backend/$(OSRM_RELEASE)/profiles/lib/access.lua
PROFILE_LIB_DESTINATION_URL:=https://raw.githubusercontent.com/Project-OSRM/osrm-backend/$(OSRM_RELEASE)/profiles/lib/destination.lua
BERLIN_URL:=https://s3.amazonaws.com/mapbox/node-osrm/testing/berlin-latest.osm.pbf
OSRM_EXTRACT:=$(TOOL_ROOT)/osrm-extract
OSRM_CONTRACT:=$(TOOL_ROOT)/osrm-contract


all: berlin-latest.osrm.hsgr

lib/destination.lua:
	mkdir -p lib
	wget $(PROFILE_LIB_DESTINATION_URL) -O lib/destination.lua

lib/access.lua:
	mkdir -p lib
	wget $(PROFILE_LIB_ACCESS_URL) -O lib/access.lua

lib/guidance.lua:
	wget $(LIB_GUIDANCE_URL) -O lib/guidance.lua

car.lua: lib/access.lua lib/destination.lua lib/guidance.lua
	wget $(CAR_PROFILE_URL) -O car.lua

clean:
	rm -f ./berlin-latest.*
	rm -f ./car.lua
	rm -rf ./lib

berlin-latest.osm.pbf:
	wget $(BERLIN_URL) -O berlin-latest.osm.pbf

berlin-latest.osrm: berlin-latest.osm.pbf car.lua $(OSRM_EXTRACT)
	@echo "Verifiyng data file integrity..."
	md5sum car.lua berlin-latest.osm.pbf > data.md5sum
	@echo "Running osrm-extract..."
	$(OSRM_EXTRACT) berlin-latest.osm.pbf -p car.lua

berlin-latest.osrm.hsgr: berlin-latest.osrm car.lua $(OSRM_CONTRACT)
	@echo "Running osrm-contract..."
	$(OSRM_CONTRACT) berlin-latest.osrm

checksum: md5sum car.lua lib/destination.lua lib/access.lua lib/guidance.lua berlin-latest.osm.pbf > data.md5sum

.PHONY: clean checksum
