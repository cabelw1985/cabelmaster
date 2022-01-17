#!/usr/bin/python
# -*- coding: utf-8 -*-
# Copyright (C) 2019 by Loki1979
import os,sys,re,zipfile,hashlib
import xml.etree.ElementTree as ET
from shutil import copyfile,rmtree

if sys.version_info.major < 3:
	sys.stdout.write("Dieses Script wurde auf Python 3.X.X ausgelegt !")
	sys.stdout.flush()
	sys.exit(0)

_script_path = os.path.normpath(os.path.dirname(os.path.realpath(sys.argv[0])))
_addons_dir = os.path.join(_script_path,"addons")
_new_repo_dir = os.path.join(_script_path,"new_repo")

def _stdout_writer(s):
	try:
		sys.stdout.buffer.write(s.encode("UTF-8"))
		sys.stdout.flush()
	except Exception as e:_stdout_writer("Stdout Writer Error :{0}".format(e))

def _new_repo_generator(addons_dir,new_repo_dir):

	_stdout_writer("Kodi Repo Gernerator Copyright (C) 2019 by Loki1979\n")
	_stdout_writer("Python Version : {0}\n\n".format(sys.version))
	_stdout_writer("Starte Repo Generator !\n\n")

	if not os.path.exists(addons_dir):
		try:
			os.makedirs(addons_dir)
			_stdout_writer("Addons Ordner erstellt :\n{0}\n\n".format(addons_dir))
			_stdout_writer("Bitte Addons Ordner fuellen ! Keine gezippten Addons ! Nur entpackte Addons !\n\n")
		except Exception as e:
			_stdout_writer("Addons Ordner konnte nicht erstellt werden !\n{0}".format(e))
			sys.exit(0)
	else:

		if os.path.exists(new_repo_dir):
			try:
				rmtree(new_repo_dir,ignore_errors=True,onerror=None)
				os.makedirs(new_repo_dir)
			except Exception as e:
				_stdout_writer("Repo Ordner konnte nicht geloescht/erstellt werden !\n{0}".format(e))
				sys.exit(0)

		_stdout_writer("Addons Ordner :\n{0}\n\n".format(addons_dir))
		_stdout_writer("Repo Ordner :\n{0}\n\n".format(new_repo_dir))
		_stdout_writer("Durchlaufe Addons und erstelle neue Repo Struktur unter :\n{0}\n\n".format(new_repo_dir))

		addon_counter = 0
		addons_xml = "<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"yes\"?>\n<addons>\n"

		addons_error_log=""
		for addon in os.listdir(addons_dir):
			if (re.search("(context|metadata|plugin|repo|resource|script|service|skin|weather)",addon) and not re.search(".zip",addon)):
				try:
					addon_xml_path = os.path.join(addons_dir,addon,"addon.xml")
					if os.path.exists(addon_xml_path):

						xml_image_paths = []
						name="";id="";version="";provider=""
						for elem in ET.parse(addon_xml_path).getroot().iter():

							if elem.tag == "addon":
								name = re.sub("\[[^\]]*\]","",elem.attrib['name'])
								id = elem.attrib['id']
								version = "-" + elem.attrib['version']
								provider =  re.sub("\[[^\]]*\]","",elem.attrib['provider-name'])
								_stdout_writer("Addon Name       : {0}\nAddon ID         : {1}\nAddon Ver        : {2}\nErsteller        : {3}\n".format(name,id,version[1:],provider))

							if elem.tag == "icon":
								if elem.text:
									xml_image_path = os.path.normpath(elem.text)
									if not xml_image_path in xml_image_paths:xml_image_paths.append(xml_image_path)

							if elem.tag == "fanart":
								if elem.text:
									xml_image_path = os.path.normpath(elem.text)
									if not xml_image_path in xml_image_paths:xml_image_paths.append(xml_image_path)

							if elem.tag == "banner":
								if elem.text:
									xml_image_path = os.path.normpath(elem.text)
									if not xml_image_path in xml_image_paths:xml_image_paths.append(xml_image_path)

							if elem.tag == "clearlogo":
								if elem.text:
									xml_image_path = os.path.normpath(elem.text)
									if not xml_image_path in xml_image_paths:xml_image_paths.append(xml_image_path)

							if elem.tag == "screenshot":
								if elem.text:
									xml_image_path = os.path.normpath(elem.text)
									if not xml_image_path in xml_image_paths:xml_image_paths.append(xml_image_path)

						if not addon == id:_stdout_writer("Namenskorrektur  : {0} = {1}\n".format(addon,id))

						if not os.path.exists(os.path.join(new_repo_dir,id)):
							os.makedirs(os.path.join(new_repo_dir,id))
							_stdout_writer("Ordner erstellt  : {0}\n".format(id))

						if os.path.exists(os.path.join(addons_dir,addon)):
							for file in os.listdir(os.path.join(addons_dir,addon)):
								if (re.search("(addon.xml|changelog.txt)",file)) or (re.search("(icon|fanart)",file) and re.search("(.png|.jpg|.jpeg|.bmp|.gif)",file)):
									copyfile(os.path.join(addons_dir,addon,file),os.path.join(new_repo_dir,id,file))
									_stdout_writer("Datei kopiert    : {0}\n".format(file))

						files_list = [];counter = int(0)
						files_list = list(os.walk(os.path.join(addons_dir,addon),topdown=False,onerror=None,followlinks=True))
						list_len = sum(len(files) for path,dirs,files in files_list)
						zip = zipfile.ZipFile(os.path.join(new_repo_dir,id,id + version + '.zip'),'w',zipfile.ZIP_DEFLATED)
						rootlen = len(os.path.join(addons_dir,addon)) + 1
						for root,dirs,files in files_list:
							for file in files:

								if not re.search("(.gitignore|.gitattributes|.DS_Store)",file):
									file_path = os.path.join(root,file)
									zip.write(file_path,os.path.join(id,file_path[rootlen:]))

								counter +=1
								percent = ("{0:.1f}").format(100 * (counter / float(list_len)))
								filled_length = int(50 * counter / list_len)
								bar = "█" * filled_length + '-' * (50 - filled_length)
								_stdout_writer("\rErstelle Zip     : {0}| {1}%%\r".format(bar,percent))
								if counter == list_len:_stdout_writer("\n")

						zip.close()
						_stdout_writer("Zip erstellt     : {0}\n".format(id + version + '.zip'))

						for image_path in xml_image_paths:
							if (os.path.exists(os.path.join(addons_dir,addon,image_path)) and not os.path.exists(os.path.dirname(os.path.join(new_repo_dir,id,image_path)))):
								os.makedirs(os.path.dirname(os.path.join(new_repo_dir,id,image_path)))
								_stdout_writer("Ordner erstellt  : {0}\n".format(os.path.dirname(os.path.join(id,image_path))))
							if (os.path.exists(os.path.join(addons_dir,addon,image_path)) and not os.path.exists(os.path.join(new_repo_dir,id,image_path))):
								copyfile(os.path.join(addons_dir,addon,image_path),os.path.join(new_repo_dir,id,image_path))
								_stdout_writer("Datei kopiert    : {0}\n".format(os.path.basename(image_path)))

						addon_xml_content = ""
						with open(addon_xml_path,"r",encoding="UTF-8") as xml:
							for line in xml.readlines():
								if not (line.find("<?xml") >= 0):addon_xml_content += line.rstrip() + "\n"
						addons_xml += addon_xml_content.rstrip() + "\n\n"

						_stdout_writer("\n")
						addon_counter +=1

				except Exception as e:addons_error_log += "Addon Name       : " + addon + "\n" + "Fehler           : " + str(e) + "\n\n"

		if addon_counter > 0:

			try:
				addons_xml = addons_xml.strip() + "\n</addons>\n"
				with open(os.path.join(new_repo_dir,"addons.xml"),"wb") as fi:fi.write(addons_xml.encode("UTF-8"))
			except Exception as e:
				_stdout_writer("Die Datei ( addons.xml ) konnte nicht erstellt werden !\n{0}".format(e))
				sys.exit(0)
			try:
				addons_xml_hash = hashlib.md5(open(os.path.join(new_repo_dir,"addons.xml"),"r",encoding="UTF-8").read().encode("UTF-8")).hexdigest()
				with open(os.path.join(new_repo_dir,"addons.xml.md5"),"wb") as fi:fi.write(addons_xml_hash.encode("UTF-8"))
			except Exception as e:
				_stdout_writer("Die Datei ( addons.xml.md5 ) konnte nicht erstellt werden !\n{0}".format(e))
				sys.exit(0)

			_stdout_writer("Addons in Repo   : {0}\n".format(addon_counter))
			_stdout_writer("Datei erstellt   : addons.xml\n")
			_stdout_writer("Datei erstellt   : addons.xml.md5\n")
			_stdout_writer("Fertig !\n\n")

			if addons_error_log:_stdout_writer("Fehler Bericht   :\n\n{0}".format(addons_error_log))

		else:_stdout_writer("Keine Addons im Addons Ordner :\n{0}\n\n".format(addons_dir))

if __name__ == "__main__":_new_repo_generator(_addons_dir,_new_repo_dir)