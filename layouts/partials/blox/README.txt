Blake Cole
25-FEB-2025

This file tracks changes made to the files residing in the directory:
/layouts/partials/blox/.

These files were copied and pasted from the corresponding directory in _vendor/.

These files override the corresponding files that reside in _vendor/.

I am tracking changes for posterity, in case things get altered or overritten.

file: resume-biography-3.html
date: 2025-02-25

----------------------------------------------------
goal:
Increase width of "Education" block on homepage.

edit:
[104] <div class="flex-auto max-w-prose md:mt-12">

changed to:
[104] <div class="flex-auto max-w-max md:mt-12">

result:
Success.

----------------------------------------------------
goal:
Increase vertical space between education instances.
Use bold typeface for institutions.
Change headings/institutions to white, degrees/text in gray.

edit:
[104] <div class="flex-auto max-w-max md:mt-12 dark:text-gray-300">
[146] <p class="course">{{ .area }}{{ with .year }}, {{ . }}{{ end }}</p>
[148] <p class="text-sm">{{ .institution }}</p>

changed to:
[104] <div class="flex-auto max-w-max md:mt-12">
[146] <p class="course dark:text-gray-300">{{ .area }}{{ with .year }}, {{ . }}{{ end }}</p>
[148] <b><p class="text-sm" style="margin-bottom:3mm;">{{ .institution }}</p></b>

result:
Success.

----------------------------------------------------
