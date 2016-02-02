# TABLE OF CONTENTS

## PART 1 - SET UP VIRTUAL ENVIRONMENT
## PART 2 - INSTALL THE VIRTUAL OS
## PART 3 - UPDATE THE BASICS (FOR THE OS)
## PART 4 - INSTALL THE REQUIREMENTS (FOR TABULA)
## PART 5 - INSTALL AND RUN TABULA (TO CONVERT PDF)
## PART 6 - CLEAN THE OUTPUT, MANUALLY (USING OPENOFFICE)
## PART 7 - CLEAN THE OUTPUT, AUTOMATICALLY (USING SED)
## PART 8 - MATCH THE CROSS-REFERENCED CELLS (USING MYSQL)
## PART 9 - OUTPUT TO A YAML FILE (FROM MYSQL)

## PART 1 - SET UP VIRTUAL ENVIRONMENT

Download & Install Virtual Box
* `>> https://www.virtualbox.org/wiki/Downloads`

Download & Install Vagrant
* `>> https://releases.hashicorp.com/vagrant/1.8.1/vagrant_1.8.1.dmg`

## PART 2 - INSTALL THE VIRTUAL OS

Navigate to the Location Where you Want Your VM
* `>> cd ../[MY_NEW_VM]`

Download Vagrant Cloud 32-bit “Precise” (for Linux-Ubuntu, includes Java Runtime Environment JRE)
* `>> vagrant box add hashicorp/precise32`
(Or check to see if it is installed:)
* `>> java -version`

Initalize a New Vagrant Virtual Machine
* `>> vagrant init hashicorp/precise32`

Start / “Spin Up” the New VM
* `>> vagrant up`

Make Sure Vagrant Is Running
* `>> vagrant -v`

“Fake”/Virtual Remote Connect to the New VM Server Environment
* `>> vagrant ssh`

## PART 3 - UPDATE THE BASICS (FOR THE OS)

Update Apt-Get
* `>> sudo apt-get update`

Install VIM (for Text Editing)
* `>> sudo apt-get install vim`

Install cURL
* `>> sudo apt-get install curl`
(Or check to see if it is installed:)
* `>> curl -V`

Install Git (for Version Control)
* `>> sudo apt-get install git`

## PART 4 - INSTALL THE REQUIREMENTS (FOR TABULA)

Install GCC C Compiler & Make & G++
* `>> sudo apt-get install gcc make g++`

Install Java Runtime Environment (JRE) for Ubuntu 14.x
* `>> sudo apt-get install default-jre`
(Or check to see if it is installed:)
* `>> java -version`

Get latest rbenv
* `>> git clone https://github.com/rbenv/rbenv.git ~/.rbenv`

Optional: compile dynamic bash extension to speed up rbenv
* `>> cd ~/.rbenv && src/configure && make -C src`

Add ~/.rbenv/bin to your $PATH for access to the rbenv command-line utility
* `>> echo 'export PATH="$HOME/.rbenv/bin:$PATH"' * `>> ~/.bash_profile`

Add rbenv init to your shell to enable shims and autocompletion
* `>> echo 'eval "$(rbenv init -)"' * `>> ~/.bash_profile`

Make Sure Changes Take Into Effect
* `>> (open new tab/window)`
* `>> vagrant ssh`

Check to Make Sure rbenv Installed
* `>> type rbenv`

* `>> git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build`

* `>> git clone https://github.com/rbenv/ruby-build.git`
* `>> cd ruby-build`
* `>> sudo ./install.sh`

Figure out where ruby is installed (may or may not be useful later on)
* `>> which ruby`
(example:) /opt/vagrant_ruby/bin/ruby
(Double-check the most recent:)
* `>> git pull`
(Triple-check the install:)
* `>> sudo ./install.sh`

## PART 5 - INSTALL AND RUN TABULA (TO CONVERT PDF)
(https://asciinema.org/a/22761 | https://github.com/tabulapdf/tabula-extractor/wiki/Using-the-command-line-tabula-extractor-tool)

Navigate Outside the rbenv Folder
* `>> cd ..`

Install jRuby
* `>> rbenv install jruby-1.7.15`
* `>> rbenv rehash`
* `>> jruby -S gem install tabula-extractor`
* `>> (should see msg: "2 gems installed")`

Install Tabula Extractor
(If you want the change PERMANENT:)
* `>> rbenv global jruby-1.7.15`
(If you want the change to go away when you log out:)
* `>> export RBENV_VERSION=jruby-1.7.15`

Check to see if Tabula is Running
* `>> tabula --help`

Move the PDF File to the Vagrant Shared Folder
* `>> (copy the PDF file to /[MY_NEW_VM])`

Convert the PDF Table into a CSV File
* `>> tabula --pages all --format=CSV --outfile=fai_part5.csv fai.pdf`

## PART 6 - CLEAN THE OUTPUT, MANUALLY (USING OPENOFFICE)

Download OpenOffice (for Mac, Linux)
* `>> (http://www.openoffice.org/download/)`
* `>> (Open the CSV file in the OpenOffice Spreadsheet Application)`

Definitions out of Column Alignment?
* `>> (Manually drag and drop Columns out of Alignment.)`

Extra Columns?
* `>> (Manually remove/delete extra blank center column.)`

Text Wrapping on Multiple Rows in "Terms" Column?
* `>> (For "Terms" column, manually consolidate text wrapping on multiple rows onto top-most row. Doesn't occur very often.)`
* `>> (NOTE: For "Definitions" column, we will automatically consolidate wrapping in the next Part of this Instructions document)`

Extra Headers & Footers?
* `>> (For Headers, manually edit/remove all Header rows. There should no Header rows in the data.)`
* `>> (For Footers, manually remove "Page #" content from rows..some of the "Page #" characters may be interspersed within a "Terms"/"Definitions" row)`

Other Inconsistencies in Data & Content?
* `>> (Inconsistent Entry Found: FedBizOpps & Federal Business Opportunities have extra data column)`
* `>> (Inconsistent Entry Found: COR = Contracting Officer's Representative however, COR = Contracting Officer's Representative; shouldn't there be an abbreviation for COTR instead?)`

Save to Force Double Quotes Around All Text Cells (will help us when consolidating the "Definitions" column wrapping issue)
* `>> (File * `>> Save As * `>> Text CSV * `>> In "Export Text File" dialog window * `>> select "Quote all text cells" checkbox`
* `>> (Use filename: fai_part6.csv)`

## PART 7 - CLEAN THE OUTPUT, AUTOMATICALLY (USING SED)

Install sed (Stream Editor) in Order to Manipulate the Text
* `>> sudo apt-get install sed`
(Or see if you already have it installed:)
* `>> sed --help`

Cell Content on Multiple Rows in Last Column? Consolidate the "Definitions" Column Due to Wrapping (from 2-Column Data)
* `>> (NOTE: Depending on file type, you may have to replace "\n" with "\r" or "\r\n". `
* `>> sed ':a;N;$!ba;s/\"\n,\"/ /g' fai_part6.csv > fai_part7.csv `

## PART 8 - MATCH THE CROSS-REFERENCED CELLS (USING MYSQL)

* `>> (Manually match in OpenOffice since is only 130+ rows; lots of variation in matching as well)`
* `>> (File * `>> Save As... * `>> filename: fai_part8.csv`

## PART 9 - OUTPUT TO A YAML FILE (FROM MYSQL)

Run Through CSV-to-YAML Converter
* `>> (https://gist.github.com/potatosalad/893073 | http://www.convertcsv.com/csv-to-yaml.htm)`

Check Your Work By Using a YAML Linter
* `>> (https://github.com/Pryz/yaml-lint | http://www.yamllint.com/)`


