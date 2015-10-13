top_srcdir=../../../..
TOP=$(top_srcdir)

include $(top_srcdir)/commands.make

PROJECTNAME=Cheesesquare
SLNFILE=./Cheesesquare.sln

UITEST_PROJECTNAME = Cheesesquare.UITests
UITEST_DIR = Cheesesquare.UITests
UITEST_APK = com.sample.cheesesquare-Signed.apk
UITEST_DLL = $(UITEST_DIR)/$(BUILDOUTPUTDIR)/$(UITEST_PROJECTNAME).dll
SAMPLE_APK = $(BUILDOUTPUTDIR)/$(UITEST_APK)

BUILDCONFIGURATION=Release
BUILDOUTPUTDIR = bin/$(BUILDCONFIGURATION)

ASSEMBLYFILE=$(PROJECTNAME).dll

BUILDOUTPUT = $(BUILDOUTPUTDIR)/$(ASSEMBLYFILE)


all: $(BUILDOUTPUT)

$(BUILDOUTPUT) : $(shell find . -type f -name '*.cs' -not -path "./obj/*" -not -path "") \
	$(shell find . -type f -name '*.xml' -not -path "./obj/*" -not -path "./bin/*") \
        $(PROJECTNAME).csproj
	
	$(NUGET) restore $(SLNFILE)
	$(XBUILD) $(XBUILD_ARGS) /p:Configuration=$(BUILDCONFIGURATION) $(PROJECTNAME).csproj

$(UITEST_DLL): 
	$(XBUILD) $(XBUILD_ARGS) /p:Configuration=$(BUILDCONFIGURATION) $(UITEST_DIR)/$(UITEST_PROJECTNAME).csproj
	
$(SAMPLE_APK): $(UITEST_DLL)
	$(XBUILD) $(XBUILD_ARGS) /t:SignAndroidPackage /p:Configuration=$(BUILDCONFIGURATION) $(PROJECTNAME).csproj

uitests: $(UITEST_DLL) $(SAMPLE_APK)
	cp $(SAMPLE_APK) $(UITEST_DIR)/$(BUILDOUTPUTDIR)/app.apk
	$(NUNIT) $(UITEST_DLL)

testcloud: $(UITEST_DLL) $(SAMPLE_APK)
	$(TESTCLOUD) submit $(SAMPLE_APK) $(TESTCLOUD_API_KEY) --devices $(TESTCLOUD_DEVICES) --series "$(TESTCLOUD_SERIES)" --locale "$(TESTCLOUD_LOCALE)" --user $(TESTCLOUD_USER) --assembly-dir $(UITEST_DIR)/$(BUILDOUTPUTDIR)

clean:
	rm -rf bin/ obj/ $(UITEST_DIR)/bin $(UITEST_DIR)/obj


.PHONY : all clean
