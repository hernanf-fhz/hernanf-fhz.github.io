# powershell advanced tools & scripting - Full course

### url

 [powershell advanced tools & scripting - Full course](https://www.youtube.com/watch?v=K4YDHFalAK8)

### 1- get started scripting

#### installing powershell 3.0

 download windows management framework 3.0
 install *FULL* .NET 4.0 framework

#### powershell 2.0
 xp et 2003


### 2- powershell's scripting language

#### quote et guillemets

" $i "
$i est évalué
' $i '
$i pas évalué

" `$i $i"
le 1er $i est pas évalué mais le second l'est

#### type cast

 $x = [int]"1"
 transforme en integer

#### strong type
 [int]$x="1"
  ok
 [integer]$x="oups!"
  marche pas car pas bon type

#### validateSet
 [validateSet("a","b","c")][string]$x="a"
   ok
 [validateSet("a","b","c")][string]$x="d"
  marche pas car pas valide

#### sql injection impossible en powershell

car ce sont des objets pas des strings qu'on manipule
la commande qu'on passe n'est pas une chaine.

$pid=get-process lssas
" process id = $pid.id"
marche pas car $pid est remplacé par $pid.ToString
 qui donne "System.Diagnostics.Process(lsass)

#### $(): évaluation de n'importe quoi dans une chaine

" process id = $(pid).id"
 process id = 740

#### @" "@ mettre tout un tas de ligne dans une variable

#### @' '@ mettre tout un tas de ligne dans un variable sans évaluer les $xxxx

donc permet de mettre un script

donc permet par exemple de créer sont propre sniplet
dans ise


#### import un csv : on obtient un PScustomobject

 donc dans les endroits ou on veut un strings il faut transformer en string

en powershell v2

 Get-Service -name bits -ComputerName (Import-csv c:\mycsv.csv | select-object -ExpandProperty ComputerName)

en powershell v3

 Get-Service -name bits -ComputerName (Import-csv c:\mycsv.csv).ComputerName

#### virtual module !!!!

 pas besoin d'importer
  on utilise et il importe pour nous la 1ere fois


#### if :

if ( ) { 
  # commands
 } elseif ( ) {
# commands
 } elseif ( ) {
# commands
 } else {
# commands
 }

ici aussi on peut assigner à une variable un statement if()
$x=if() {} ...

#### switch

* 1ere version

 $status=3
 switch(status) {
 0 { $status_check='ok' }
 1 { $status_text='warning' }
 2 { $status_text='jammed' }
 3 { $status_text='overheated' }
 default { $status_text='unknown' }
 }
 # on affiche
 $status_text

* 2eme version : on peut assigner un statement donc
$status=3
 $status_text = switch(status) {
 0 { 'ok' }
 1 { 'warning' }
 2 { 'jammed' }
 3 { 'overheated' }
 default { 'unknown' }
 }
 $status_text

#### loop : ForEach

do {  } while ( )

while () { }

ForEach() {  } 

FOR (;;) { }
### 3- simple scripts and functions

#### passer des parameters : $args supporté

#### passer des parameters: param(param1,param2, etc.)

 param(
	[string]$ComputerName='client',
	[string]$Drive='c:'
 )

 et on peut faire:
 ./monscript.ps1 -ComputerName server -Drive 'd:'

#### [CmdletBinding()] et script

 à placer devant param

#### [CmdletBinding()] et fonction : 

permet d'avoir plein de Cmdlet dans 1 fichier

  dans 1 fonction à placer aussi devant param

  si la fonction s'appelle "t"
  lancer le script : . .\myscript.ps1
  et on a la fonction qui marche comme une cmdlet
    PS c:\> t

 attention : ISE Powershell fait le travail (dot source) sans le dire
 ce qui est perturbant


#### param et mandatory

 param(
 	[Parameter(Mandatory=$True)]
	devant le parametre qui est obligatoire



#### profile: script  lancé à chaque session

  ils sont lancés avec . .\script.ps1 (dot source)

#### get-psdrive et effacer des fonctions !

get-psdrive donne les drives habituels, mais aussi la registry, pleins de choses
 dont les fonctions

on peut donc faire
 remove-item Function:LeNomDeMaFonction

#### ISE powershell et ctrl-t

 ouvre un autre environnement powershell dans ISE !


### 4- advanced functions

 dans une advanced function:

 Begin{} lancé 1 fois au début
 Process{} lancé autant que d'objet dans le pipeline
 End{} lancé 1 fois à la fin

 on peut ainsi
 function verb-noun {
 
  param(
	[parameter](valuefrompipe=$true)
	[int]$x
	)
 
	Begin{ $total=0 }
	Process { $total+=$x }
	End { "total = $total" }
 }

 1..3 | verb-noun
  total = 6

 
 dans ISE on peut l'obtenir avec ctrl-J et choisir Advanced_function
      ou Advanced_function - complete


 voir aussi snippet : extrait de texte réutilisable par powershell ISE

 Process n'est pas en parallele mais en seriel
 car en dot net il faut gérer la concurrence pour etre thread safe
 et ce serait le job du developpeur powershell ce qui est trop compliqué

#### invoke-command : lance en parallele

invoke-command -computername (get-content servers.txt) { ... }
se fait en parallele sur les N serveurs
 c'est eux (les inventeurs de powershell) qui gèrent la concurrence

 ont la 
 * todo list
 * progress list 
   faite de 32 ou 64 serveurs mais on peut changer cela
   et là en parallele
 * done list


#### [switch] : dans param

     [switch]$errorlog,

 
on peut alors 

   if ($errorlog) { }

#### write-verbose
 alors faut mettre -verbose pour avoir les write-verbose !

 une bonne idee et de mettre des verifications dans Begin
 avec des write-verbose

#### pour les choses à traiter
 dans le Process block
 car permet de recevoir du pipe des données.

#### hashtable:


#### fin de declaration (statement terminator): return et ;
 les  2 le sont
 donc si on met qu'une instruction, alors on peut laisser le retour chariot

#### powershell est insensible à la case:
 donc peut mettre $c ou $C , $VMs ou $VMS,  etc.

#### hastable : 
 sans [ordered] donne un ordre non predictible !
 mais coute 1 peu plus cher à executer
  $prop=[ordered]@{
  'computername'=$c
  'OS Name'=$c.caption
  etc.
  }

c'est un objet de Name et Value

#### psobject: mieux pour avoir property dont on veut imposer les noms

$obj=New-Object -TypeName PSObject -property $prop

permet de formater les choses dans le meme formalise que le reste
et utiliser les autres methods/modules/etc. de powershell


### 5- more on parameters

#### decorations: on delegue à quelqu'un (MS) le travail

ex: mandatory=$true
  ainsi 1 seul message erreur que les gens vont reconnaitre (sans meme vraiment lire)
  ainsi erreur dans pleins de langage sans avoir à s'en charger

#### ISE: ctrl-n 

 nouveau tab

#### clip: recevoir le pipe dans le presse-papier

 get-help *toto | clip

#### entree de parametre, demande de saisie d'un tableau, sortir: ligne blanche

 param(
	[Parameter(mandatory=$true)]
	[string[]]$Computername
 )
 
alors si le script lancé sans arg, n'arrete pas de demander un ComputerName
 pour sortir il faut une ligne blanche (retour chariot)

#### Parameter(ValueFromPipeline=$true, ValueFromPipelineBypropertyName=$true)

 on peut alors envoyer dans cette variable le contenu d'un pipe
 
 get-content c:\computers.txt | get-CompInfo


 bien penser le code dans le process block pour qu'il recoive les données

#### dans parameter : HelpMessage

     [Parameter(HelpMessage="un message d'aide")]

donc quand on doit taper le parameter
on a le message : pour de l'aide taper !?
 et si on tape !? alors le message s'affiche

####  alias: apres parameter
     [Alias("HostName")]

permet que dans le pipeline l'association se fasse quand meme
 meme si la personne qui envoie utilise un autre nom que celui qu'on a choisi

####  validateSet : apres parameter, est très utile
      [parameter ... ]
     [validateSet("dc1","dc2")]
     [String[]]$ComputerName

 en plus intellisense à la saisie, sait afficher l'ensemble de validation
  et avec ctrk-space on a le menu

#### validateCount : apres parameter
 [validateCount(0,2)]
     [String[]]$ComputerName

 0, 1 ou 2 ComputerName mais pas plus !

#### validatepartern: devant la variable, apres parameter

     [validatepattern(expression reguliere)]

#### dans ISE: #unMorceauDeCmdeTapeePrecedemment + Tab
     on peut remonter dans l'historique et retrouver sa commande

#### validateScript + un script block

     evalue le script block et si true alors ok

     ex: checker si un path qu'on fourni est correct

     [validateScript({test-path $path})]
     $path

     et donc si le chemin n'existe pas il rale !
    

### 6- writing help

#### comment based help
facile, rapide 
mais permet pas de faire update-help sur ces comment based
 par contre xml on peut ... mais plus lourd
 par contre dans module on peut ... mais plus lourd

##### place du bloc comment
on trouve les comment based help dans les bloc comment
<#
.Synopsis
  le format court de l'aide
#>
MaFonctionAvancee() { }

Attention ne marche que si le bloc comment est collé est à la fonction
ou separée d'une *SEULE* ligne blanche au plus

Mieux d'utiliser ctrl-j et fonction avancée pour trouver toute la syntaxe
(.Synopsis, .Description, .Parameter, .Example (en mettre plein) etc.)

#### pleins d'exemple c'est mieux : 
vygosky et activity theories:
 pourquoi il faut mettre pleins d'exemples
 va leur permettre d'augmenter leur compétence

#### avoir pleins d'exemple

 lancer la commande
 copier/coller la commande + le resultat dans le comment bloc avec
 devant .Example



#### exemples
get-help MaFonctionAvancee
 on voit le synopsis

get-help MaFonctionAvancee -full
 il donne les parametres pour l'appeler, etc.

get-help MaFonctionAvancee -parameter ComputerName
 il donne juste le param et les alias

#### un commentaire devant un parametre
 et il apparait dans la description obtenue par le get-help !!!

pour plus d'infos
get-help about_coment_based_help

 


### 7- error handling

#### $ErrorActionPreference
actuellement vaut continue

#### sur la ligne de commande : -ErrorAction SilentlyContinue -ErrorVariable MyError
 on peut les abreger -EA et -EV
 ainsi on continue si erreur mais on met les erreurs dans $MyError

#### -ErrorAction Stop
 il s'arrete

#### -ErrorAction Inquire
 pose la question si on continue, arrete ou suspend

 avec suspend on a prompt
 PS C:\>>
  on debuger
  puis sortir avec exit

#### -ErrorVariable e

 $e[0] la 1ere erreur

 $e[0] | gm
 est un objet

 $e[0] | ft *
 affiche l'erreur et pas les properties ... mais c'est normal

 il faut faire 
 $[0] | ft * -force

 et du coup on voit qu'il y a targetobject

 on peut faire $[0].targetobject
 et recuperer les objets qui ont planté sur la 1ere erreur

#### error view: NormalView et categoryView
 categoryView plus court

 dir variable:*view*

 on voit que $ErrorView="NormalView" mais on peut mettre "CategoryView"

#### Qualified error id
 on peut chercher sur internet
 le reste du message peut être localisé

#### -force

 est là pour dire, oui on  veut le faire (malgré les protections de powershell)

#### d'autres preferences
trouver toutes les preferences
 dir variable:*pref*


#### try  & catch

 try {
     noun-verb -ErrorAction Stop -ErrorVariable CurrentError 
}
 catch {
       le code pour signer erreur ou la corriger
 }

#### pour tester des erreurs sans risque sur windows

 pas de process impaire
 donc
  get-process 11,13 | stop-process
 va planter et on peut tester les erreurs	


#### ecrire dans l'eventlog

 write-Errolog -LogName Application -Source Untruc -EntryType Information -message "ce qu'on veut par exemple le programme XXX a tourné"

 (il faut enregistrer la source "Untruc" mais c'est simple)

#### montrer que l'automatisation economise du temps

 dans chaque script, faire le write-ErrorLog precedent "programme XXX a tourné"
 et en collectant tous les messages de ce type et ayant 1 table disant qu'à chaque fois cela économise tant de temps alors on a un moyen de montrer le gain de temps pris

### 8- tools that make changes

#### I was a tool, now I'm a tool maker
 blague car I'm a tool == quelle quiche je suis

#### impact

 chaque cmdlet a un impact
  low, medium, high
 ainsi à chaque fois que quelque chose a un impact high alors demande confirmation

 mais on peut dire que confirm impact soit à medium
 ainsi les cmdlet ayant un impact medium vont demander une confirmation
$confirmPreference='medium'

##### impact, cmdletbinding, SupportShouldProcess

 [cmdletbinding(SupportShouldProcess=$true,
	confirmImpact='medium')]

 permet d'utiliser -whatIf -Verbose -Confirm
 * -whatif : dit ce qu'il ferait mais le fait pas
 * -Verbose : dit ce qu'il va faire et le fait
 * -Confirm : demande confirmation

 mais il faut aussi utiliser
 if ($psCmdlet.ShouldProcess("sur quoi ", "ce qu'on va faire")) {
   action
 }

 shouldprocess est un API
 ainsi si $psCmdlet.ShouldProcess retour true ou false
       false: ne pas faire l'action si -whatif
       true : on fait l'action si -verbose
       on retour true ou false : en fonction du choix par l'utilisateur si -confirm


### 9- script and Manifest modules

#### sauvegarder un script en : .psm1
 ensuite possible de faire: import-module .\MonScript.psm1

#### emplacement pour ses modules
 ne pas mettre dans c:\windows\system32\WindowsPowerShell\v1.0\Modules
 
 mettre dnas c:\users\monprofil\Documents\WindowsPowerShell\Modules

