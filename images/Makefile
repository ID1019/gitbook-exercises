LATEXMK = latexmk
PDFLATEX = pdflatex


all : avl1.png avl2.png splay1.png splay2.png splay3.png splay4.png


%.pdf : %.tex 
	$(LATEXMK) -pdf -pdflatex=$(PDFLATEX) -shell-escape $<

%.png : %.pdf
	convert -density 300 $< -quality 90 +profile icc $@

clean:
	rm -f *.aux *.log *.fdb_latexmk *.fls *.*~ *~


realclean: clean
	rm -f *.png
	rm -f *.pdf


