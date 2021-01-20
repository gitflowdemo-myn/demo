# GitFlow

## Main og Develop branch

![](img/1.png?raw=true "master og develop branch")

Der findes to branches som er konstante.
I main branch ligger hele release historikken. Den seneste version der ligger i main branch er den version der lige nu er deployed til produktion.
develop er den branch nye features bliver integreret ind i. Nye feature branches bliver udviklet med denne branch som base og bliver integreret ind i denne branch når feature er udviklet færdig.
<br/><br/><br/>

## Feature branch
![](img/2.png?raw=true "feature branch")

Hver ny feature/story skal udvikles i sit eget branch. Feature branch bliver lavet ud fra den seneste develop branch (ikke main).

Når en feature er udviklet færdig bliver den merged ind i develop igen. En feature branch skal aldrig have noget direkte at gøre med main.
  

### Feature-branch navngivning

    [TEAMNAVN]-[Jira Story ID]-[kort-beskrivende-fritekst]

Ex:  
    
    MYN-123-prototype-af-feature

Brug bindestreg (-) som ord adskiller i den beskrivende tekst.
<br/><br/><br/> 

## Release branch

![](img/3.png?raw=true "release branch")

Når develop er klar til release bliver der lavet et nyt branch ud fra develop branch. Dette er et release branch.
release branch fungere som en feature freeze og der kan derfor ikke tilføjes nye features til denne branch. 
De eneste ændringer der laves på denne branch er bug fix, dokumentation og andet nødvendigt for en release.

Når release branch er klar til at gå i produktion bliver den merged ind i main og tagged med et versionsnummer.

Efter release branch er gået i produktion bliver den merged tilbage i develop. På den måde kommer evt. bugfixes og ændringer lavet i release branch med i develop.
<br/><br/><br/>



## Hotfix branch
![](img/4.png?raw=true "hotfix branch")

Denne branch bruges til tidskritiske ændringer/rettelser direkte i produktion. Hotfix branch fungere på samme måde som feature og release branches, forskellen er at hotfix bliver lavet ud fra main branch. Dette er den eneste branch der direkte har noget med main branch at gøre.

Når hotfix er udviklet færdig skal den merges ind i både main, develop og en evt, release branch.

<br/><br/><br/>


# GitFlow i praksis

## Clone repository
Et eksisterende repository kan hentes ned lokalt ved at "clone". Rent praktisk henter man hele projektet med alle branches og commit historik.

Vælg en mappe som repository hentes ned i. Repository vil blive hentet ned i en mappe opkaldt efter repository.

    git clone [url]


## Opret nyt feature branch
Et ny feature branch skal oprettes ud fra develop branch. Skift derfor først til develop branch:
    
    git checkout develop

Ud fra develop branch oprettes nyt branch navngivet efter [Feature-branch navngivning](#Feature-branch-navngivning)

    git branch MYN-123-ny-feature-branch

Skift til det nye branch:

    git checkout MYN-123-ny-feature-branch

Det er også muligt at oprette nyt branch og skifte til det samtidig:

    git checkout -b MYN-123-ny-feature-branch


## Push Feature branch
Som udganspunkt behøver feature branches ikke blive pushed. Der kan dog være grunde til at pushe feature branch.
Eksempelvis hvis man ønsker at feature skal være synlig for andre i forbindelse med en pull request. Feature branch kan også pushes hvis man vil have adgang til det fra en andan udviklings maskine.  
Da feature branch kun findes lokalt skal git have at vide at dette branch nu skal trackes upstream:

    git push --set-upstream origin MYN-123-ny-feature-branch

Feature branch er nu pushed og andre vil kunne pulles af andre.



## Afslut et feature branch
Når feature branch er færdig skal den merges tilbage i develop. Det gøres ved først at skifte til develop branch:

    git checkout develop

Da det lokale develop branch sandsynligvis ikke har nogen ændringer kan man her hente den seneste version af develop fra online repository:

    git pull origin 

Herefter kan feature branch merges ind i develop:

    git merge MYN-123-ny-feature-branch

Hvis det lokale develop branch var up to date med online develop branch vil der sandsynligvis ikke være nogen konflikter der skal rettes.  
Er alting i orden kan det lokale develop branch pushes til online develop branch:

   git push origin

Feature branch er nu merged ind i develop og feature branches som ikke længere skal bruges kan nu slettes.


## Opret Release branch

Når develop er klar til release bliver der lavet et nyt branch ud fra develop branch. Dette er et release branch.
release branch fungere som en feature freeze og der kan derfor ikke tilføjes nye features til denne branch. 
De eneste ændringer der laves på denne branch er bug fix, dokumentation og andet nødvendigt for en release.


Efter release branch er gået i produktion bliver den merged tilbage i develop. På den måde kommer evt. bugfixes og ændringer lavet i release branch med i develop.

    git checkout develop
    git checkout -b release/0.1.0


Når release branch er klar til at gå i produktion bliver den merged ind i main og tagged med et versionsnummer.

    git checkout main 
    git merge release/0.1.0
    git checkout develop
    git merge release/0.1.0


## Opret Hotfix branch
Hotfix oprettes på samme måde som de andre branches, dog bliver den branched fra main og ikke fra develop.

    git checkout main
    git checkout -b hotfix_branch

Ligesom et release branch bliver hotfix merged ind i både main og develop.

    git checkout main
    git merge hotfix_branch
    git checkout develop
    git merge hotfix_branch
    git push origin
    

# Eksempler på brug:


## Feature branch

    git checkout main
    git checkout -b develop
    git checkout -b feature-branch
    
    # feature branch udvikles

    git checkout develop
    git merge feature-branch


## Release branch

    git checkout main
    git merge develop
    git branch -d release/0.1.0

    # release klargøres

    git checkout main 
    git merge release/0.1.0
    git checkout develop
    git merge release/0.1.0


## Hotfix branch

    git checkout main
    git checkout -b hotfix-branch

    # der udvikles og commmites
    
    git checkout develop
    git merge hotfix-branch
    git checkout main
    git merge hotfix-branch


# Andre git features

## Check status
Man kan tjekke status på det lokale repositury med kommandoen:

    git status

Denne komando giver et overblik over hvilke filer der er belvet ændret, hvilke filer der bliver tracked m.m.  
Har man tilføjet nye filer som ikke bliver tracked kan de tilføjes til **staging area** så de kan blive tracked af Git.

## Staging

Fra projekt mappen kan man tilføje filer til **staging area** med kommandoen `git add`.

Tilføj en enkelt specifik fil:  

    git add file.cs

Tilføj flere specifikke filer:  

    git add file1.cs file2.cs file3.cs

I stedet for at tilføje filer enkeltvis kan alle filer i projektet tilføjes til staging area på en gang:  

    git add .

Som standard tilføjes alle filer og mapper til staging area.

## Commit

Når alle ønskede filer er tilføjet **staging area** kan man lave en commit af projektet. Der vil nu blive skabt et snapshot af projektet som det ser ud med de tilføjede ændringer.   
Sammen med commit skriver man en kort tekst der fortæller overordnet hvad der er blevet ændret eller tilføjet. Teksten bruges efterfølgende til at identificere netop denne commit i Git historiken.

    git commit -m "commit tekst"

Har man lavet flere ændringer som hver fortjener deres egen commit gentager man processen med at tilføje de relevante filer til **staging area** og commit.

## Commit log/historik

Se alle commits lavet på projektet.

    git log

Loggen vil vise hvem der har foretaget commit, tidspunkt for commit og den beskrivele der er givet commit.   
Samtidig kan man også se en commit hash


## Slet branch
    
Slet lokalt branch:

    git branch -d branch-navn

Forece slet:

    git branch -D branch-navn

Slet remote branch:

    git push origin --delete branch-navn

## Tagging
Et branch kan tagges ved:

    git tag -a v0.1.0 -m "Version 0.1.0"

Se alle oprettede tags:

    git tag

Vis et bestemt tag:

    git show v0.1.0
    
# test
