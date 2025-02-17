ifeq ($(JAVA_HOME),)
  $(warning No JAVA_HOME found, JNI building will fail.  Please set JAVA_HOME when using JNI)
  exit 1
endif

VWLIBS := -L../vowpalwabbit -l vw
STDLIBS = $(BOOST_LIBRARY) $(LIBS)
JAVA_INCLUDE = -I $(JAVA_HOME)/include

ifeq ($(UNAME), Linux)
  JAVA_INCLUDE += -I $(JAVA_HOME)/include/linux
endif
ifeq ($(UNAME), FreeBSD)
  JAVA_INCLUDE += -I $(JAVA_HOME)/include/linux
endif
ifeq "CYGWIN" "$(findstring CYGWIN,$(UNAME))"
  JAVA_INCLUDE += -I $(JAVA_HOME)/include/linux
endif
ifeq ($(UNAME), Darwin)
  JAVA_INCLUDE += -I $(JAVA_HOME)/include/darwin
endif

jni_SRCS = $(shell find src/main/c++ -name "*.cc")
jni_OBJS = $(jni_SRCS:.cc=.o)

all:
	cd ..; $(MAKE)

test:
	cd ..; $(MAKE) test

things: pom_version target/vw_jni.lib

pom_version: pom.xml
	ver=$$(grep AC_INIT ../configure.ac | cut -d '[' -f 3 | cut -d ']' -f 1) && \
	newVer=$$(perl -e "@a=split('\.', '$$ver'); \$$a[2]++; print(join('.', @a))") && \
	perl -pi -e "s/(\s*)<version>.*-SNAPSHOT/\1<version>$$newVer-SNAPSHOT/" pom.xml

target/vw_jni.lib: $(jni_OBJS) ../vowpalwabbit/main.o ../vowpalwabbit/libvw.a
	mkdir -p target;
	$(CXX) -shared $(FLAGS) -o $@ $^ $(VWLIBS) $(STDLIBS) $(JAVA_INCLUDE)

%.o: %.cc  %.h
	$(CXX) $(FLAGS) -c $< -o $@ $(JAVA_INCLUDE)

%.o: %.cc
	$(CXX) $(FLAGS) -c $< -o $@ $(JAVA_INCLUDE)

	-include $(jni_SRCS:.cc=.o)

.PHONY: clean
clean:
	rm -f target/vw_jni.lib
	rm -f $(jni_SRCS:.cc=.o)
