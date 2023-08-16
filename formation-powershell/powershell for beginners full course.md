# powershell for beginners full course.md

### url

 [ powershell for beginners full course ](powershell for beginners full course.md)

### 1. don't fear the shell 0:00:00

Jeffrey Snover: inventeur du powershell
 s'est inspiré de OS400 et DCL

Jason Helmick

#### à savoir

* commandlet : verb-noun
* get-alias
  ou gal
     gal *sv*
     etc.


#### help

#### mise à jour:

 update-help
  on peut aussi sauvegarder (save-help) et

#### help

get-help get-service
 affiche tout d'un coup

help get-service
 affiche comme man avec un more ce qui est plus facile

#### search

get-help G*Adcomputer*
  trouve les commandes en rapport avec Adcomputer

#### verb

get-verb

#### detail, examples, full, online, ShowWindow

get-help get-service -Detailed
   beaucoup plus d'infos
   en fait get-help sans rien donne une info condensée

get-help get-service -Full
   tout !

get-help get-service -Examples
   des examples

get-help get-service -online
  ouvre un browser et regarde en ligne

get-help get-service -ShowWindow
  ouvre une fenetre avec la doc
  avec le bouton settings tout activer pour que toutes les fonctionnalités
  soient là


#### on peut abréger grâce aux positional parameters (comme en DCL)

 get-service -name bits
 ou
 get-service bits

#### conseil: avoir le petit doigt au dessus de la touche tab !

 car on va l'utiliser bcp !
 car permet de completer les commandes , les parametres et meme les valeurs à donner à certains paramètres

#### help about_* :

des infos générales très intéressantes

#### utiliser le point-virgule

 pour séparer des commandes

### pipe : |

#### envoie dans le pipe non pas du texte: mais des objets


#### comparer les process de 2 machines:

get-process | export-clixml -path c:\good.xml

sur une autre machine
compare-object -Reference-Object (import-clixml c:\good.xml) -Difference-Object (get-process) -Property Name

affiche les colonnes: Name et SideIndicator
ex:
 Name			SiteIndicator
 ----			-------------	
 calc			=>
 notepad		=>

ces process tournent sur l'autre machine

#### difference entre export-  et convertTo

 convertTo on continue à envoyer dans le pipe
get-service | convertTo-html -Property Name,Status | out-file c:\test.htm

 export- on envoie dans un fichier et c'est tout c'est fini
 	 export- c'est un convertTo suivi d'un out-file

#### parametre -WhatIf:

 ne fait rien mais affiche ce qu'il ferait.
 Présent sur toutes les commandes.

#### parametre -confirm

 demande confirmation


### in the pipe : objects

propriétés et méthodes

#### at the end :

text (some properties displayed)
 mais il faut afficher du texte le plus tard possible
 et garder les objets le plus longtemps possible
 car tres difficile de remonter de texte à objet

#### get-member :

 get-service | gm

affiche :
 * le type de l'objet
 * tous les propriétés
 * et méthodes

permet de pleinement utiliser les objets

#### select-object ou select:

 permet de sélectionner des propriétés, les trier

 get-childitem | select -property Name,Lenght | sort -Property lenght -Descending

#### objet:
failiter la vie: exemple xml

##### casting :

$x= [xml](cat c:\r_and_j.xml)

$x.gettype()
XmlDocument


$x.play

$x.play.act[0].scene[0].speech
 les speech de la 1ere scene du premier acte de Romeo et Juliette ici

##### si pas d'index d'un tableau: tout le contenu
$x.play.act.scene.speech
 tous les speechs de toutes les scenes et actes
$x.play.act.scene.speech | group speaker |  sort count


##### dot syntax: à partir v3.0 seulement

 sinon "Select -expend propriete1 | Select -expend propriete2 | etc.

#### get-history

 historique des commandes

#### comparaison, operateurs

 get-help *comparaison*
ou 
 get-help *operator*

##### $_

 c'est aussi $PSItem

##### site web:

[powershell.org](https://www.powershell.org)

#### where {}

 1. assigne l'objet venant du pipe à une variable : $PSItem
 2. evalue le code entre {}
 3. si retourne true alors passe l'objet dans le pipe


exemple

 gps | where { $_.Handles -ge 1000}

et version simplifiée sans {}: mais fait moins de choses
 where proprieté comparaison valeur

 gps | where Handles -ge 1000


### module 6 : pipeline deeper

dans le pipe, celui qui recoit s'occupe des objets 
*  byProperty
*  byValue
*  on peut aussi creer une propriété pour qu'elle est le bon nom
*  on peut utiliser les scripts parameters

#### byValue:

ex:
 get-service | stop-service

 marche car get-service envoie des ServiceControllers
 (cf. get-service | gm )

 marche car get-stop a un parametre -inputObject ServiceController[]
 	accept pipeline input ? true (ByValue)
 (cf. get-help stop-service) 
 
 (on remarque ici que les propriétés ne portent pas les mêmes noms 
     Name pour get-service
     et input-Object pour dir )

#### byPropertyName:

 ex:
  calc
  get-process calc | dir

 dit ou se trouve calc !

 marche car dir a un parametre qui est nommé "Name" exactement comme get-process
 et qui accepte les entrees pipeline justement par PropertyName
 	accept pipeline input ? true (ByPropertyName)
  (cf. get-help dir)
  (cf. get-process | gm )

 MS souvent defini des Alias afin de nous faciliter le travail
 et permettre ce qui précède.

 Cependant s'il n'y a pas d'Alias on peut creer sa propriété supplémentaire:
  cf. la suite

#### creer une property avec le bon nom quand il n'y en a pas

pour que cette commande marche:
  get-adcomputer -filter * | get-service

pour l'instant ne peut marcher car il n'y a ni byValue ni byPropertie qui fonctionne

mais si on arrive à creer un parametre ComputerName dans le flux de sortie de get-adcomputer, alors get-service qui a ce param pourra l'utilser (byPropertyName)


on remarque que l'on cree des proprietes en faisant simplement

get-service -name bits | select -filter naem,stitus
  naem	    	       stitus
 ------		       -------

get-service -name bits | select -filter naem,stitus  | gm
 on voit qu'il a cree 2 propriétés de plus naem et stitus !

donc on fait, en utilisant la syntaxe des hash:

get-adcomputer -filter * | Select -Property name,@{name='CompterName';expression={$_.name}}

marche aussi comme ca
get-adcomputer -filter * | Select -Property name,@{n='CompterName';e={$_.name}} | 

marche aussi 
get-adcomputer -filter * | Select -Property name,@{label='CompterName';expression={$_.name}}

ou comme ca :
get-adcomputer -filter * | Select -Property name,@{l='CompterName';e={$_.name}}

finalement on peut faire ce qu'on voulait sur N ordinateurs:
get-adcomputer -filter * | Select -Property @{name='CompterName';expression={$_.name}} | get-service -name bits
 
 on voit s'afficher le service bits de plusieurs machines.

#### ExpandParameter et ( ).name

 on voudrait faire
  get-adcomputer -filter * | get-wmiObject -class wmi32_bios

 mais marche pas car
  get-wmiObject ne prend rien en pipeline !

 mais
  get-wmiObject -class wmi32_bios | gm
 nous montre qu'il attend sur le parametre ComputerName une string

 cela ne marchera pas 
  get-wmiObject -class wmi32_bios -computerName (get-adcomputer -filter * | select -Property Name )
 car get-adcomputer rend des objet ADComputer. Or -computerName veut une "string" !
 
 Au passage on voit que	(...) est executé avant tout le reste.

 il faut faire (powershell v2):
  get-wmiObject -class wmi32_bios -computerName (get-adcomputer -filter * | select -ExpandProperty Name )

 ou mieux v3:
  get-wmiObject -class wmi32_bios -computerName (get-adcomputer -filter *).name


 A savoir au passage:
 get-wmiObject est remplacé par get-cimInstance qui lui accepte les pipeline inputs!

#### script parameters:

encore mieux, on peut faire:
  get-adcomputer -filter * | get-wmiObject -class wmi32_bios -computerName {$_.name} 

quand un parametre ne prend pas de script block et qu'on en trouve un
 alors c'est un script parameter
1. on envoie dans le pipe les ADComputer
2. on execute le script block {}
3. on assigne au parametre la valeur retournée par le script block
4. on lance get-wmiObject


### remoting

communication encrypté (toujours)
fait du wsman
mais on peut aussi faire du SSL au dessus (2 fois encrypté!)

enable-PSremoting
1 gpo : Windows Remote Management	
mais depuis win2012 est mis on par defaut

  
#### enter-PSSession

pour l'interactif:

enter-PSSerssion -ComputerName RemoteSrv
[RemoteSrv] PS C:\

#### invoke-command

 pour les blocks de scripts

invoke-command -ComputerName s1,s2,dc {get-eventlog -LogName System -new 3}
 rend un seul tableau synthétisant les 3 derniers logs System des 3 machines

#### comment ca marche

 1. ouvre une connection sur la machine distante
 2. lance powershell
 3. execute le code
 4. serialise les objets
 5. les envoie à travers le réseau
 6. déserialise et recrée les objets
    attention sont des objets différents des originaux
    mais tournent partout (ex: sur linux j'ai pas server exchange, mais les objets qui viendrait d'un serveur distant exchange je pourrais les manipuler


#### powershellwebaccess: gateway pour se connecter depuis internet à nos machines!

on installe le feature sur une machine
 install-windowsfeature powershellwebaccess
on installe une webapp dans un IIS
 install-pswebapplication -UseTestCertificate
on configure la sécurité
 Add-pswaAuthorizationRule [user] [destination] [configurationName]
puis on utilise
 https://lamachine/pswa
  demande compte, mot de passe, et machine sur laquelle on veut se connecter
  et si on a les droits cela marche on a powershell depuis un browser! 

#### on peut lancer une commande en simultané sur N serveurs

invoke-command -ComputerName s1,s2,dc {get-eventlog -LogName System -new 3} | sort timewritten | format-table timewritten,message -AutoSize

#### PSComputerName est ajouté dans les objets dans ce cas:

invoke-command -ComputerName s1,s2,dc {get-Volume} | Sort SizeRemaining
 on voit une colonne PSComputerName qui a été ajouté

#### ExecutionPolicy

 protege les utilisateurs de scripts mauvais

#### Path

 pour executer le code dans son répertoire, comme sous Unix:
 .\myScript.ps1


### se préparer pour l'automatisation

#### GET/SET Execution Policy

* Restricted:
  par défaut; ne lance pas de script.

* Unrestricted et Bypass:
  à éviter.

* AllSigned
pour executer, il faut que script soit signé et qu'on approuve le signateur
 que le script soit écrit localement ou téléchargé depuis internet
 le mieux c'est AllSigned

* RemoteSigned
verifie qu'il n'y ait pas de code dans la partie signature (evite malware)
et s'il est local: pas de signature
 s'il est téléchargé d'internet: il doit y avoir une signature
 RemoteSigned c'est bien pour commencer
 en 2012R2 c'est par defaut.
 
#### new-selfsignedcertificate
 pour creer son certif

 get-psdrive

 on trouve cert:

 dir cert:\CurrentUser -Recurse -CodeSignedCert -OutVariable a
on voit les certif
et $a est affecté

$a
 affiche tous les certifs

$cert=$a[0]

Set-AuthenticodeSignature -Certificate $cert -FilePath .\test.ps1

.\test.ps1
 il demande si on fait confiance à la personne qui a signé

 
#### variables: peuvent contenir des objets

 $MyVar=get-service bits
 $MyVar| gm

 $MyVar.status
 Running
 
 $MyVar.Stop()
 $MyVar.Refresh()
 $MyVar.status
 Stopped

 toujours un $

 on peut avoir n'importe quoi comme nom de variable
 ${ this is a test &#@ 123 } = "WOW"

 on peut aussi avoir un nom de fichier
 1..5 > test.txt
 ${c:\test.txt}
 1
 2
 3
 4 
5 

 permet de stocker le contenu d'une variable dans un fichier
 et de se l'échanger entre progr ou machine


#### write-host versus write-output

 ne pas (trop) write-host utiliser car n'envoie pas d'objet dans le pipe
  il ne fait qu'envoyer sur la console

 Write-output $var | gm 
  écrit l'objet sur la console
  + envoie un objet sur le pipe

write-warning
  en jaune

write-error
  en rouge
  et affiche une erreur de type powershell


### Automation in scale, Remoting:

#### PSsession:
 
 un environnement PS qui reste ouvert
 
 $sessions=new-pssession -computername dc

 get-pssession
  on voit notre session
 
 icm -Session $Sessions {$var=2}
 icm -Session $Sessions {$var}
 affiche 2

#### deploiement multiple en parallele

$servers= 's1','s2'

$sessions= new-pssession -ComputerName $servers

icm -session $sessions {Install-WindowsFeature web-server}
 installation en parallele !

copier la page web par defaut default.htm

$servers | foreach {copy-item default.htm -Destination \\$_\c$\inetput\wwwroot}

#### inutile d'installer pleins de module sur sa machine !

 difficile de tout avoir sur sa machine
 avec les bonnes versions !


 il suffit d'importer les modules via une pssession !

$s=new-pssession -computerName DC
import-pssession -session $s -Module ActiveDirectory -Prefix remote

 get-help *remoteAD*

 on retrouve les commandes prefixées avec AD !!

 get-remoteADComputer -filter *
 on récupère les noms de l'AD sans avoir installé le module !


 get-command fait par le import
  (sorte de get-help plus structuré)
 construit une sorte de proxy
 ET LANCE le code sur la machine DISTANTE !!!

 depuis powershell v cmdlet peut etre .net ou powershell
 et là c'est powershell généré automatiquement

 dans le cas d'une migration de exchange d'une version à une autre
 plus facile suffit de 2 import-pssession

 ou si pleins de credentials différents
 suffit de faire des pssession

### ISE

#### dans ISE la console a été améliorée

 on a completion mais aussi des popups, des couleurs, etc.

 par exemple ctrl+space presente 1 popup avec toutes les possibilités du mot qu'on est en cours de taper.

#### ctrl-r: 

 ctrl-r passe de l'onglet console à l'onglet editeur

#### param

 param (
   $ComputerName='localhost',
   $bogus
 )

 et du coup le script accepte les parameters
  -ComputerName
  -bogus
 et get-help marche !

 param (
   [strings[]]$ComputerName='localhost',
   $bogus
 )

 et là il sait qu'on peut passer plusieurs strings

#### CmdletBinding()

 [CmdletBinding()]
 param (
   [string[]]$ComputerName='localhost',
   $bogus
 )

 et maintenant get-help montre en plus en arg CommonParameters

 [CmdletBinding()]
 param (
       [Parameter(Mandatory=$True)]
   [string[]]$ComputerName,
   $bogus
 )
 et du coup il faut absolument positionner ComputerName, sinon il le demande

#### commentaires et get-help

 <#
 .Synopsis
   short description
 .Description
   long description
 .Parameter ComputerName
   as imply by the name 
 .Example
  un ex
 #>
 [CmdletBinding()]
 param (
        [Parameter(Mandatory=$True)]
   [string[]]$ComputerName='localhost',
   $bogus
 )

 et du coup cela marche :
 get-help cefichier.ps1
 get-help cefichier.ps1 -Full


#### pour avoir un modele de tout cela: ctrl-j dans ISE

 choisir Cmdlet (advanced function) 
 ou Cmdlet (advanced function) - complete

 et il met un exemple de tout cela (commentaire, param, etc.)
 qu'il suffit de modifier !

#### pour pouvoir utiliser ses fonctions dans powershell : function et . (dot)

on definie un fonction avec function 
 <#
 .Synopsis
   short description
 .Description
   long description
 .Parameter ComputerName
   as imply by the name 
 .Example
  un ex
 #>
 [CmdletBinding()]
 param (
        [Parameter(Mandatory=$True)]
   [string[]]$ComputerName='localhost',
   $bogus
 )

 function get-XXX {
 le code
}


on execute:
 . \cefichier.ps1

 on peut executer get-XXX car il est dans le stack courant (comme en Unix)

 la completion marche 

 et les Common Parameters aussi

 get-XXX -computerName serverN -OutVariable a 

 $a contient le retour de la fonction

#### module: facon powershell v2
 
 dans ISE, sauvegarder en psm1

 importModule Monfichier.psm1

#### $env:PSModulePath

 afin de le voir mieux  
 $env:PSModulePath -split ";"

 ainsi notre répertoire pour les modules powershell
 %HOME%\Documents\WindowsPowerShell\Modules\
  
 creer son répertoire
  y mettre son psm1

 ainsi le module est connu (sans import , sans install)

 get-help get-XXX

