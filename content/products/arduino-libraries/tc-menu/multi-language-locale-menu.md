+++
title = "Multi language locale based menu for Arduino and mbed"
description = ""
tags = [ "arduino", "display-driver", "embedded-menu", "library" ]
type = "blog"
date = "2023-05-30"
author =  "dave"
menu = "tc-menu"
banner = "/products/arduino-libraries/images/electronics/arduino/tcMenu/generatorui-locale-language-configure.png"
githublink = "https://github.com/davetcc/tcMenu"
referenceDocs = "/ref-docs/tcmenu/html/index.html"
weight = 2
toc_needed = true
+++

TcMenu 4.0 supports multi-language menus based on [resource bundles](https://www.baeldung.com/java-resourcebundle) which are effectively properties files containing language translations. The designer lets you set up translations on a per-locale basis for the app name, each menu item, and even for additional strings in your application. Once enabled the properties files are turned into a series of C++ include header files that you can choose between using a TC_LOCALE_?? setting described below.

## Enabling and setting up languages

To enable the bundle support, or setup additional languages from the "Code" menu select "Configure Locales". This will bring up the following dialog where you can add and remove locales from the list of available languages. Please note that designer will not erase locale files that already exist to avoid accidental data loss.

{{< figure src="/products/arduino-libraries/images/electronics/arduino/tcMenu/generatorui-locale-language-configure.png" alt="Configure locale dialog showing enabled and available locales" title="Locale Configuration dialog" >}}

### Adding and removing languages

To select a locale choose the language first, and then if required you can add a country as well, once both are selected click the ">>" button to move it selected. You can only automatically remove items before a file is created as discussed above.

## Properties file storage

Let's take a project file that has been localized to both English and French. We normally always treat English as the default language. We will end up with an i18n directory at the same level as the `emf` project file with each translation within it.

    projectDirectory
        projectName.emf
        i18n
            project-lang.properties
            project-lang_fr.properties

Within one of the properties files, any localized menu items will have their translations, here are some examples:

    # Created by TcMenu to hold menu translations, will always be written in UTF-8
    menu.3.enum.1=Item2
    menu.3.enum.0=Item 1
    menu.3.name=Enum
    menu.5.name=Settings
    menu.3.enum.2=ChangeMe
    menu.1.name=Analog
    menu.1.unit=V
    menu.2.name=Float
    menu.4.name=Power
    menu.8.name=YesNo
    menu.7.name=RGB
    menu.6.name=Action
    project.name=Adafruit Dashboard

We can see that each menu items name is localized and the localization is done by ID. Enumeration values, Analog unit names and most other strings can be localized from within the designer UI.

When we localize a string, it's name/unit/value in the `emf` file will show with a `%` at the start, this means it is localized and the translation is within the bundle. The exception is `%%` which escapes the `%` symbol.  

        "unitName": "%menu.1.unit"

To escape a `%` at the start of the text:

        "unitName": "%%"

## Editing items in designer

In designer, at the right side of the name edit control you can now select the locale that you wish to edit in. If it is left as '--' this means do not localize, otherwise you'll create entries into the locale table.

{{< figure src="/products/arduino-libraries/images/electronics/arduino/tcMenu/generatorui-locale-language-change.png" alt="Locale selection for editing items - Generator UI" title="Locale selection during editing" >}}


## I18N and code generator

As of 4.0 there are extended save locations, where you can generate files into a "generated" directory. This is recommended when combined with locales, as the number of files increases with each language. To change save location simply select the Root item in the tree and change the save location:

{{< figure src="/products/arduino-libraries/images/electronics/arduino/tcMenu/generatorui-locale-save-locations.png" alt="Possible save locations for both your and the generated code" title="Choosing a save location" >}}


In the generated output directory there will be several new files. Firstly `projectName_langSelect.h` that will include the right language header file based on a compile time flag, then each of the language files following the pattern `picoAdafruitDashboard_lang.h`. Here's an example:

    generated
        projectName_lang.h
        projectName_lang_fr.h
        projectName_langSelect.h
        projectName_menu.cpp
        projectName_menu.h

Looking inside a language header file we can see it follows a pattern to convert the above properties keys into definitions (capitalize and separate by underscore):

    #define TC_I18N_MENU_2_NAME "Float"
    #define TC_I18N_MENU_8_NAME "YesNo"
    #define TC_I18N_MENU_5_NAME "Settings"
    ... skipped
    #define TC_I18N_PROJECT_NAME "Adafruit Dashboard"


Always only include `projectName_langSelect.h` as this will select the right locale. Locales use the standard language and country format. For example to choose global French set the follow compile flag:

    TC_LOCALE_FR

The lack of such a compile flag means use the default locale. Even most TcMenu internal strings are now localized and use the same locale definition file, there are presently translations into English, French, Slovak, German and Czech. Any other translations would be greatly welcomed, see the tcMenuLib github repo.
