---
comments: true
image:
    feature: old-days-of-technology.jpg
date: 2010-09-05 08:47:43+00:00
layout: post
slug: descomprimir_ficheros_zip_en_django
title: Descomprimir ficheros zip en Django
locale: es_ES
wordpress_id: 546
categories:
- Web
tags:
- archivo
- descomprimir
- django
- extract
- fichero
- python
- script
- unzip
- zip
---

Para un nuevo proyecto en el que me estoy embarcando, he tenido que hacer un script que permitiese **subir archivos zip** en Django y los **descomprimiese**. Tras un inicio dubitativo, me he puesto a buscar alguna aplicación o código que ya trajese esta opción hecha y al final he terminado haciendo una mezcla entre código obtenido de [django-photo-albums](http://code.google.com/p/django-photo-albums/) y [este trozo de código](http://djangosnippets.org/snippets/1893/) publicado en DjangoSnippets.

El script escribe el fichero enviado por un formulario POST en disco, comprueba que el fichero es efectivamente un **zip** y que no está **corrupto** y lo descomprime, almacenando su contenido en la ruta MEDIA_ROOT configurada en el fichero settings.py del proyecto.

Es bastante simple, pero espero que pueda servir de ayuda a alguien y, sobre todo, que alguien me pueda **sugerir mejoras** para incluirlas luego en el proyecto.



{% highlight python %}
# -*- coding: utf-8 -*-

import tempfile, os
import settings
from django.http import HttpResponse
from django import forms
from zipfile import ZipFile, BadZipfile
from django.shortcuts import render_to_response

class UploadFileForm(forms.Form):
	title = forms.CharField(max_length=50) # No sirve para nada
	file  = forms.FileField()

	# Almacena en disco el fichero
	# Comprueba que el zip no está corrupto
	# Devuelve el path absoluto a dicho fichero
	def clean_file(self):
		def ffile_path(uploaded_file):
			'''  Converts InMemoryUploadedFile to on-disk file so it will have path. '''
			try:
				return uploaded_file.temporary_file_path()
			except AttributeError:
				fileno, path = tempfile.mkstemp()
				temp_file = os.fdopen(fileno,'w+b')
				for chunk in uploaded_file.chunks():
					temp_file.write(chunk)
				temp_file.close()
				return path

		path = ffile_path(self.cleaned_data['file'])

		try: # Comprobación de que el fichero no está corrupto
			zf = ZipFile(path)
			bad_file = zf.testzip()
			if bad_file:
				raise forms.ValidationError(_('El fichero "%s" del ZIP está corrupto.') % bad_file)
			zf.close()
		except BadZipfile:
			raise forms.ValidationError('El fichero subido no es un ZIP.')

		return path

	def process_file(self):

		# Ruta donde se encuentra el fichero
		zip_filename = self.cleaned_data['file']

		# Lugar donde se alojarán los ficheros descomprimidos
		dirname = settings.MEDIA_ROOT

		zip = ZipFile(zip_filename)

		lista_ficheros = []

		# Recorremos todos los ficheros que contiene el zip
		for filename in zip.namelist():

			ruta_total = os.path.join(dirname, filename)

			# Si es un directorio, lo creamos
			if filename.endswith('/'):
				try: # Don't try to create a directory if exists
					os.mkdir(ruta_total)
				except:
					pass
			# Si es un fichero, lo escribimos
			else:
				outfile = open(ruta_total, 'wb')
				outfile.write(zip.read(filename))
				outfile.close()
				lista_ficheros.append(ruta_total)

		zip.close()
		os.unlink(zip_filename)

		return lista_ficheros

# Vista que muestra el formulario o gestiona la petición POST de éste
def upload_file(request):
	if request.method == 'POST':

		form = UploadFileForm(request.POST, request.FILES)

		# Si el formulario el válido, proceso el fichero
		if form.is_valid():

			zipdata = request.FILES['file']
			title = request.FILES['file'].name

			listado = form.process_file()

			# Aquí podríamos trabajar con los ficheros subidos
			# cuyas rutas totales se encuentran en listado

			# Esta es la respuesta que enviamos al usuario una vez
			# finalizado el proceso
			return HttpResponse("Subido!")

	else:
		form = UploadFileForm()
	return render_to_response('upload.html', {'form': form})
{% endhighlight %}
