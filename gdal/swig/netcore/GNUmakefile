
include ../../GDALmake.opt
include csharp.opt

all: build

BINDING = csharp
include ../SWIGmake.base

SWIGARGS = -Wall -I../include -I../include/$(BINDING)

LINK = $(LD_SHARED)
LINK_EXTRAFLAGS =
OBJ_EXT = o
ifeq ($(HAVE_LIBTOOL),yes)
LINK = $(LD)
LINK_EXTRAFLAGS = -rpath $(INST_LIB) -no-undefined -version-info $(LIBGDAL_CURRENT):$(LIBGDAL_REVISION):$(LIBGDAL_AGE)
OBJ_EXT = lo
endif

OUTPUT = runtimes/$(RID)/native

CSHARP_MODULES = libgdalcsharp.$(SO_EXT) libogrcsharp.$(SO_EXT) libgdalconstcsharp.$(SO_EXT) libosrcsharp.$(SO_EXT)
CSHARP_OBJECTS = const/gdalconst_wrap.$(OBJ_EXT) gdal/gdal_wrap.$(OBJ_EXT) osr/osr_wrap.$(OBJ_EXT) ogr/ogr_wrap.$(OBJ_EXT)

clean:
	-rm -f ${CSHARP_MODULES} *.$(OBJ_EXT) *.config *.dll *.mdb *.exe

veryclean: clean
	-rm -f -R const/*.cs const/*.c gdal/*.cs gdal/*.cpp osr/*.cs osr/*.cpp ogr/*.cs ogr/*.cpp Data

generate: interface
interface:
	./mkinterface.sh

build: ${CSHARP_OBJECTS} ${CSHARP_MODULES} gdal_csharp

sign:
	sn -k gdal.snk

install:
	@echo "No installation to be done"

$(CSHARP_MODULES): lib%csharp.$(SO_EXT): %_wrap.$(OBJ_EXT)
	$(LINK) $(LDFLAGS) $(CONFIG_LIBS) $< -o $@ $(LINK_EXTRAFLAGS)

%.$(OBJ_EXT): %.cpp
	$(CXX) $(CFLAGS) $(GDAL_INCLUDE) -c $<

%.$(OBJ_EXT): %.cxx
	$(CXX) $(CFLAGS) $(GDAL_INCLUDE) -c $<

%.$(OBJ_EXT): %.c
	$(CC) $(CFLAGS) $(GDAL_INCLUDE) -c $<

gdal_csharp:
	mkdir -p $(OUTPUT)
	cp ../../.libs/libgdal.so.20.2.0 $(OUTPUT)
	cp .libs/lib*.so.20.2.0 $(OUTPUT)

	mv $(OUTPUT)/libgdalcsharp.so.20.2.0 $(OUTPUT)/gdal_wrap.so
	mv $(OUTPUT)/libogrcsharp.so.20.2.0 $(OUTPUT)/ogr_wrap.so
	mv $(OUTPUT)/libosrcsharp.so.20.2.0 $(OUTPUT)/osr_wrap.so
	dotnet restore
	dotnet build -c Release


pack:
	dotnet pack -c Release