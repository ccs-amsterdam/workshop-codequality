# CCS Amsterdam workshop on code quality

*“From script to software”*

The general goal of the workshop is to discuss tips and best practices to improve the quality of code produced as part of our research.
Many of us write analysis scripts and/or tools as part of our daily work.
Some of theses scripts are one-off exploratory scripts for which the quality of the code doesn't really matter.
In many cases, however, it would be good if the code is readable and re-usable,
either because you have to look at it again a year later for an R&R or follow-up study,
or because the script or tool is useful for other researchers. 

Programme:

1. [Introduction](intro.md): What & why?
2. [Structure](structure.md): Functions, classes, modules, and command line arguments
3. [Documentation](documentation.md): Comments, docstrings, and documentation
4. [Typing](typing.md): Publishing your package on pypi/CRAN
5. [Testing](testing.md): Unit tests, test-driven development, and github actions

**Example and Exercise:** For this workshop, we will use a (intentionally badly written) version of the worlde game published here: https://github.com/vanatteveldt/eldrow/blob/main/eldrow.py. At the end of the workshop you will have transformed that code into a readable and re-usable program using functions, docstring, type hints, and unit tests. Although for such simple code it might be a bit overkill, this will showcase all the building blocks and principles needed to write quality code in your daily work. Comparing the different 'solutions' will also be a nice starting point for discussing the choices made and learning from each other.  

