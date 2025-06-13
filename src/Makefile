SRCDIR = $(shell pwd)
SOURCES = $(shell find $(SRCDIR) -name '*.cxx')
HEADERS = $(shell find $(SRCDIR) -name '*.h')
OBJS = $(patsubst %.cxx, %.o, $(SOURCES))

INCLUDE += -I$(SRCDIR) -I$(SRPROXY_INC) -I/usr/include/curl
LDLIBS += -L$(SANDRECO_LIB) -lStruct -L/usr/lib64 -lcurl

# enable TMS unless otherwise specified
# (set ENABLE_TMS=0 in the make command line)
ENABLE_TMS := 1
ifneq ($(ENABLE_TMS), 1)
	CXXFLAGS += -DDISABLE_TMS
endif


# Automatically enable SAND if SANDRECO_INC or SANDRECO_LIB are set
ifneq ($(origin SANDRECO_INC), undefined)
	INCLUDE += -I$(SANDRECO_INC)
	SAND_OPT = -DENABLE_SAND
endif

ifneq ($(origin SANDRECO_LIB), undefined)
	LDLIBS += -L$(SANDRECO_LIB) -lStruct
	SAND_OPT = -DENABLE_SAND
endif


LIBNAME = ND_CAFMaker
LIBS = $(LIBDIR)/lib$(LIBNAME).so
BINS = $(BINDIR)/makeCAF

# build the library and the binaries
all: $(BINS)

# testing...
test: $(BINDIR)/testHDF

# make an object for any .cxx requested
%.o : %.cxx $(if $(wildcard %.h),%.h,)
	$(CXX) $(CXXFLAGS) $(ROOTFLAGS) $(INCLUDE) -fPIC $(SAND_OPT) -o $@ -c $< #compile

# should *really* make a .d rule for the objects
# to get their dependencies on the headers right, but ... not now
$(LIBDIR)/lib$(LIBNAME).so: $(OBJS) $(HEADERS)
	$(CXX) $(CXXFLAGS) $(ROOTFLAGS) -shared -o $@ $(OBJS)

$(BINDIR)/makeCAF: $(LIBS) makeCAF.C
	$(CXX) $(CXXFLAGS) $(ROOTFLAGS) $(INCLUDE) -L$(LIBDIR) -l$(LIBNAME) $(LDLIBS) -o $@ makeCAF.C

$(BINDIR)/testHDF: $(LIBS) testHDF.C
	$(CXX) $(CXXFLAGS) $(ROOTFLAGS) $(INCLUDE) -L$(LIBDIR) -l$(LIBNAME) $(LDLIBS) -o $@ testHDF.C


clean:
	rm -f $(OBJS)
	rm -f $(LIBS)
	rm -f $(BINS)
	rm -f $(wildcard AutoDict_*)
