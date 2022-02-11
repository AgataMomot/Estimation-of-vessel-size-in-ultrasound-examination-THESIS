#!/usr/bin/make -f

#WERSJA 1.0e

#NAZWA GLOWNEGO PLIKU
TEXF	 	= Praca
#ZOSTAWIAC LOGi?
LOG		= tak
#NAZWA EWENTUALNEGO PLIKU Z OKLADKA
COVER		= cover
#DOMYSLNY CEL (dvi|ps|pdf)
GOAL		= pdf

LATEX	 = latex
BIBTEX	 = bibtex
PDFLATEX = pdflatex
PS2PS    = pstops
PSSELECT = psselect
PS2PDF	 = ps2pdf
GS	 = gs
XPDF	 = xpdf -fullscreen
GIT	 = git

#taka będzie nazwa przyjęta przez latexa jako podstawa dla pliku dvi (jeśli jest rozszerzenie, to się go pozbądź)
RAWNAME	:= $(shell echo "$(TEXF)" |sed -e 's/\.[^\.]\+$$//')

#nazwy plików generowanych/pośrednich/docelowych
DVIF	:= $(RAWNAME).dvi
PSF	:= $(RAWNAME).ps
PDFF	:= $(RAWNAME).pdf
LOGF	:= $(RAWNAME).log

#pretekst do powtórnego uruchomienia
RERUN		= "(There were undefined references|Rerun to get (cross-references|the bars) right)"
BIB		= "No file.*\.bbl|Citation.*undefined"

#pliki do usunięcia
CLEANF		:= *.aux *.blg *.out *.ilg *.ind *.bbl *.brf *.toc *.lof *.lot
MRPROPERF	:= *.ps *.dvi *.pdf *.log
ifeq ("$(LOG)", "nie")
	CLEANF	+= *.log
endif

#
all: $(GOAL)

dvi: rmdvi _dvi
ps:   rmps _ps rmdvi
pdf:  rmpdf _pdf

bib: phd.bib
_dvi: skrot $(DVIF) clean
_ps:  skrot $(PSF)  clean 
_pdf: imepspdf skrot $(PDFF) clean

rmdvi:
	-rm -f $(DVIF)
rmps:
	-rm -f $(PSF)
rmpdf:
	-rm -f $(PDFF)	

cph:
	@rm -f phd.bib
phd.bib:
	@cat [A-Z]*.bib > phd.bib
	@cat bib.d/* >>phd.bib

pspdf: ps 
	$(PS2PDF) $(PSF)
	-rm -f $(PSF)
#	-find ./ -maxdepth 1 -name '*.ps' -exec ps2pdf "{}" \;
#	-rm -f *.ps
psgspdf: ps
	@cat $(PSF) | $(GS) -sDEVICE=pdfwrite -sOutputFile=$(PDFF) -dNOPAUSE -q -
	-rm -f $(PSF)

%.dvi: %.tex
	$(LATEX) $<
#sprawdź, czy potrzeba bibliografii	
	egrep -c $(BIB) $(LOGF) >/dev/null && $(BIBTEX) $(RAWNAME) && $(LATEX) $< ; true
#sprawdź, czy było coś nie tak 
	egrep -c $(RERUN) $(LOGF)  >/dev/null && $(LATEX) $< ; true
	egrep -c $(RERUN) $(LOGF)  >/dev/null && $(LATEX) $< ; true
#ostrzeżenia
	egrep -i "(Reference|Citation).*undefined" $(LOGF) ; true

%.ps: 	%.dvi
	dvips $< -o $(PSF)

%.pdf: %.tex
	$(PDFLATEX) $<
	egrep -c $(BIB) $(LOGF) >/dev/null && $(BIBTEX) $(RAWNAME) && $(PDFLATEX) $< ; true
	egrep -c $(RERUN) $(LOGF) >/dev/null && $(PDFLATEX) $< ; true
	egrep -c $(RERUN) $(LOGF) >/dev/null && $(PDFLATEX) $< ; true
	egrep -i "(Reference|Citation).*undefined" $(LOGF) ; true

skrot:
	-scripts/genskrot	
imepspdf:
	-scripts/imeps
clean:
	-rm -f $(CLEANF)
mrproper: clean
	-rm -f $(MRPROPERF)
bak:
	-scripts/bak
cover:
	-scripts/preparecover $(TEXF) $(COVER)
show: 
	@$(XPDF) $(PDFF)
	
gitr:
	@$(GIT) add *.tex *.bib *.sty Makefile