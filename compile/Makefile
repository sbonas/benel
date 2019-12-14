
MAIN_OBJ := a
OTHER_OBJS := b

DYNLINKER := /lib64/ld-linux-x86-64.so.2
CRTLIBDIR := /usr/lib/x86_64-linux-gnu/

OUTLIBDIR := libs

%.o: %.c
	gcc -c $^

$(OUTLIBDIR)/lib%.so: %.c
	mkdir -p $(OUTLIBDIR)
	gcc $^ -shared -fPIC -o $@

static: $(addsuffix .o,$(MAIN_OBJ) $(OTHER_OBJS))
	ld $^ -o $@ -dynamic-linker $(DYNLINKER) $(CRTLIBDIR)/crt1.o $(CRTLIBDIR)/crti.o -lc $(CRTLIBDIR)/crtn.o

dynamic: $(addsuffix .o,$(MAIN_OBJ)) $(patsubst %,$(OUTLIBDIR)/lib%.so,$(OTHER_OBJS))
	# Using rpath, we make the dynamic executable non-portable, unless OUTLIBDIR's location is in
	#     LD_LIBRARY_PATH or in DYNLINKER's search paths.
	ld $< -o $@ -dynamic-linker $(DYNLINKER) $(CRTLIBDIR)/crt1.o $(CRTLIBDIR)/crti.o -lc -rpath $(OUTLIBDIR) -L$(OUTLIBDIR) $(addprefix -l,$(patsubst $(OUTLIBDIR)/lib%.so,%,$(filter-out $<,$^))) $(CRTLIBDIR)/crtn.o 

clean:
	-rm -f $(addsuffix .o,$(MAIN_OBJ) $(OTHER_OBJS))
	-rm -rf $(OUTLIBDIR)
	-rm -f static
	-rm -f dynamic

.PHONY: clean

