NAME=stratuslab-federated-images

all : $(NAME).tex $(NAME).bib
	pdflatex $(NAME).tex
	bibtex $(NAME)
	pdflatex $(NAME).tex
	pdflatex $(NAME).tex

clean : 
	rm -f *.aux *.bbl *.blg *.lof *.log *.lot *.out $(NAME).pdf *.toc *~ *.gz

