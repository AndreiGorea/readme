# Integrarea Backstop în Hygge

Acest README oferă un ghid detaliat pentru echipa de QA privind utilizarea integrării Backstop în instrumentul Hygge, pentru teste de regresie vizuală automate. Integrarea permite crearea și rularea testelor automate cu Backstop, cu rezultate structurate în cadrul instrumentului Hygge.

## Pasul 1: Autentificare în Hygge

Fiecare membru al echipei QA va utiliza un cont de utilizator creat de admin pentru a se autentifica în Hygge. După autentificare, utilizatorii au acces la pagina principală cu un meniu care conține: 
1. Dashboard
2. Runners
3. Users
4. Settings
5. Sign out

![](https://i.ibb.co/j6zBN48/meniu.png)

## Pasul 2: Lucrul cu Runners

Focalizăm pe opțiunea Runners. În secțiunea Runners, utilizatorii vor găsi un buton plus care deschide un meniu dropdown cu elemente din Runners, precum
1. Products
2. Features
3. Properties

![](https://i.ibb.co/bdd863D/1.png)

### Adăugarea de Produse

1. Apăsați pe "Products" pentru a accesa pagina cu produse existente, cum ar fi COLLECT, CHANNEL, GLOBAL.
2. Pentru a adăuga un nou produs, introduceți denumirea produsului în câmpul de input "Name" și utilizați butonul "Add".

![](https://i.ibb.co/nnFyGQ0/1.png)

**Tot pe aceasta pagina daca facem click pe vreun produs, se deschide pagina Product details, unde putem sa vedem in partea de jos a paginii ce Feature are fiecare produs.**

![](https://i.ibb.co/MPwtQ5k/1.png)

### Adăugarea de Features

1. Features, daca apasam pe Features se deschide pagina cu Features, aici noi putem crea un Feature nou, sau putem accesa unul existent.

![](https://i.ibb.co/VpYsB3x/1.png)

Pentru a crea un feature nou, alegeți produsul dorit, introduceți denumirea în câmpul "Name" și apăsați butonul "Add".

Pentru a crea un feature nou la dropdownul mai jos de parent product alegem produsul de care avem nevoie, de exemplu COLLECT, CHANNEL, GLOBAL, scriem o denumire mai jos la campul Name si apasam butonul Add, apoi ea va aparea in partea dreapta la feature si putem sa apasam pe ea si se va deshide detalii acestui feature unde se intampla toata magia si noi scriem testele noastre.

### Adăugarea de steps în Feature

1. In partea dreapta jos a paginii este Suggestions, asta este un exemplu cum trebuie sa arate un test scris cu ajutorului instrumentului Cucumber.

![](https://i.ibb.co/6b9D9Tm/1.png)

1. Sub "Features details", găsiți câmpul "New step" în partea dreaptă.
2. Apăsați în câmp și tastați un spațiu.
3. Va apărea o listă cu stepurile disponibile pentru a fi selectate.

**!! Putem vedea stepurile de asemenea daca scriem denumirea lor in camp, va lucra ca un search !!**

####Prima ce trebuie sa facem, este ca sa cream sriptul onBefore, ca exemplu avem acest onBefore.js fisier

```javascript
// Exemplu de onBefore.js
module.exports = async (page, scenario, vp) => {
    await page.goto("https://crossnet-test.crossinx.com:8443/crossnet/trust/loginpage");
    await page.waitForTimeout("200");
    await page.click("a[class='accept-cookies']");
    await page.type("#loginName", "miCollectyT44", { delay: 5 });
    await page.waitForTimeout("200");
    await page.type("#password", "miCollectyT44!", { delay: 5 });
    await page.waitForTimeout("200");
    await page.click("input.btn.big-btn");
    await page.waitForTimeout("500");
    await page.waitForSelector("body[data-page-initialized='true']", { visible: true, });
    await page.waitForTimeout("1000");
}; 
```
**Notă:** Fiecare rând de cod reprezintă câte un step 


#### In total avem 3 stepuri care primesc ca parametru la metoda un numar diferit de parametri

Avem step cu **un parametru**:

```json
When I add key {string} with value {string} for onBefore script
                            ↓
When I add key "waitForTimeout" with value "200" for onBefore script

```
Avem step cu **doi parametri**: 
```json
When I add key {string} with value {string}, {string} for onBefore script
                            ↓
When I add key "waitForSelector" with value "body[data-page-initialized='true']",
"{ visible: true, }" for onBefore script
```
Avem step cu **trei parametri**: 
```json
When I add key {string} with value {string}, {string}, {string} for onBefore script
                            ↓
When I add key "type" with value "#loginName", "miCollectyT44", "{ delay: 5 }" 
for onBefore script
```
**Primul string este ceea ce urmeaza dupa "page."  de exemplu "goto", "waitForTimeout", "click", "type", etc.**

**Deja urmatorele stringuri vin valorile de exemplu, un link, un selector, etc.**

**Uite la exemplu cu onBefore.js de mai sus**

####Acesta este un exemplu de onBefore:

```json
When I add key "goto" with value "https://crossnet-test.crossinx.com:8443/crossnet/trust/loginpage" for onBefore script
When I add key "waitForTimeout" with value "200" for onBefore script
When I add key "click" with value "a[class='accept-cookies']" for onBefore script
When I add key "type" with value "#loginName", "miCollectyT44", "{ delay: 5 }" for onBefore script        
When I add key "waitForTimeout" with value "200" for onBefore script
When I add key "type" with value "#password", "miCollectyT44!", "{ delay: 5 }" for onBefore script        
When I add key "waitForTimeout" with value "200" for onBefore script
When I add key "click" with value "input.btn.big-btn" for onBefore script
When I add key "waitForTimeout" with value "500" for onBefore script
When I add key "waitForSelector" with value "body[data-page-initialized='true']", "{ visible: true, }" for onBefore script
When I add key "waitForTimeout" with value "1000" for onBefore script
```

Dupa ce scriem pasii pentru onBefore, putem sa ne apucam si de scrierea scenariilor, pentru asta vom avea nevoie de urmatorul step:
```json
When I add extension key {string} with value {string}
```
1. In loc de primul string poate fi "label", "url", "hoverSelectors", "delay". 
2. Al doilea string este valoarea, delay poate fi 1000 de exemplu, url poate fi un link, hoverSelector deja un selector cum ar fi "#showImporterPopup"

**Acesta este un exemplu de test!**
```json
When I add extension key "label" with value "CTCollect"
When I add extension key "url" with value "CTCollectMySuppliersUrl"
When I add extension key "hoverSelectors" with value "#showImporterPopup"
When I add extension key "delay" with value "1000"
```

**Dupa ce se va rula testul se va genera acest backstop.json**
```json
"scenarios": [
    {
      "misMatchThreshold": 0.1,
      "requireSameDimensions": true,
      "postInteractionWait": 1000,
      "label": "TestHoverButton",
      "url": "https://crossnet-test.crossinx.com:8443/crossnet/en_DE_xcollector/layouts/finx/mynetwork/mysuppliers",
      "hoverSelectors": ["#showImporterPopup"],
      "delay": "1000"
    }
  ],
```
```json
"misMatchThreshold": 0.1,
"requireSameDimensions": true,
"postInteractionWait": 1000,
```
Daca nu adaugam acesti 3 pasi ei se vor adauga automat la inceput de test

**Dupa ce scriem un test trebuie de adaugat un step obligatoriu de la New step**

```json
Then I add backstop scenario step from extensions
```
**Acest step grupeaza toti pasii la un loc si creaza un test, acest step trebuie adaugat dupa fiecare test scris de exemplu:**

```json
 	   When I add extension key "label" with value "CTCollect"        
        When I add extension key "url" with value "CTCollectMySuppliersUrl"
        When I add extension key "hoverSelectors" with value "#showImporterPopup"
        When I add extension key "delay" with value "1000"      
 
        Then I add backstop scenario step from extensions
		
		When I add extension key "label" with value "CTCollect"        
        When I add extension key "url" with value "CTCollectMySuppliersUrl"
        When I add extension key "hoverSelectors" with value "#showImporterPopup"
        When I add extension key "delay" with value "1000"      
 
        Then I add backstop scenario step from extensions
		
```

Aici avem 2 teste, putem adauga mai multe teste in acelasi timp

**Un alt pas obligatoriu este pasul care formeaza scriptul onBefore.js**

```json
Then I create onBefore scenario script from steps
```
**El se pune la sfarsitul tuturor testelor, de exemplu:**

```json
		When I add extension key "label" with value "CTCollect"        
        When I add extension key "url" with value "CTCollectMySuppliersUrl"
        When I add extension key "hoverSelectors" with value "#showImporterPopup"
        When I add extension key "delay" with value "1000"      
 
        Then I add backstop scenario step from extensions
		
 	   When I add extension key "label" with value "CTCollect"        
        When I add extension key "url" with value "CTCollectMySuppliersUrl"
        When I add extension key "hoverSelectors" with value "#showImporterPopup"
        When I add extension key "delay" with value "1000"      
 
        Then I add backstop scenario step from extensions

		Then I create onBefore scenario script from steps
	```


**Ultima comanda care finiseaza tot este comanda care ruleaza comanda de care avem nevoie:**
1. testCommand = test
2. approveCommand = approve

```json
Then I run backstop command "testCommand
```
**!!! Numaidecat apasam butonul "Submit" mai jos de Content pentru a salva toti pasii adaugati !!!**

După adăugarea tuturor pașilor, rulați testul apăsând butonul "Add queue" situat în partea dreaptă sus, sub "Runner templates".

### Vizualizarea testelor executate

1. Ca sa vedeti testul executat, accesați istoricul runnerilor rulați deschizând "Runners" în partea stângă din meniu. În secțiunea "Runner templates", selectați runnerul dorit, de exemplu, "Generic runner". Apoi, în "Runner queues", alegeți ultimul runner rulat, care este afișat cu data și ora, de exemplu, "01/10/2024 22:43:52". Apăsați pe acesta pentru a deschide detaliile asociate.

![](https://i.ibb.co/98J0hM0/1.png)

Dupa asta se va deschide o pagina cu:
1. Runner queue details - detalii generale cum ar fi data, ora rularii, durata, statutul
2. Steps - toti pasii 
3. Backstop report - tabel cu cele mai importante elemente din runner

**Ca sa vedem imaginea care a fost creata acum apasam pe "View Image" mai jos de "Test image"
Ca sa vedem imaginea de referinta apasam pe "View Image" mai jos de "Reference image"**

![](https://i.ibb.co/M2zJyVW/1.png)

Ca sa salvati o imagine de referinta trebuie de rulat inca odata acelasi test doar ca de data asta trebuie sa rulati comanda approve care in proprietati este scris ca **approveCommand**
```json
Then I run backstop command "approveCommand".
```
Si va duceti la Runners si cautati din nou runerele, doar ca de data asta nu il vom deschide pe primul de pe lista dar pe al doilea adica precedentul

###Properties

Proprietatile joaca un rol de label, ca in loc de lucruri care se schimba cum ar fi un selector, sau un link mare noi inlocuim cu un cuvant cheie care va face referinta la contentul propriu zis, de exemplu:

```json
crossnet-login=/crossnet/trust/loginpage
crossnet-world-login=/crossnet/en/layouts/finx/trust/login
CTCollectArchiveUrl=/crossnet/en_DE_xcollector/layouts/finx/modules/archive/adocumentlist
CTCollectMySuppliersUrl=/crossnet/en_DE_xcollector/layouts/finx/mynetwork/mysuppliers
CWTCollectCampaigns=/crossnet/en/layouts/finx/campaign/mycampaigns
pageRenderBody=body[data-page-initialized='true']
```
Cand le adaugam atunci cand cream testul le punem forma lor din partea stanga a egalului in ghilimele, de exemplu: 
When I add extension key "url" with value "CTCollectMySuppliersUrl"

**Proprietatile se afla in Runners din partea stanga dupa ce apasam pe plus se va deschide dropdown cu Products Features si Propreties
Apasam pe Propreties si vom vedea o lista intreaga de proprietati**

![](https://i.ibb.co/RbM7c3T/1.png)

De asemnea aici putem adauga proprietati noi selectand la Parent product un produs, mai jos scriem un nume la Name si apasam Add, el va aparea in partea dreapta.
Putem deschide oricare proprietate si adauga una care vrem noi de exemplu

```json
staging-de=https://crossnet-staging.crossinx.com:8524
```

In partea stanga de egal e label si in partea dreapta valoarea propriu zisa>
 
**Nota:** nu este nevoie de ghilimele!
Dupa scriem o proprietate o adaugam cu butonul Submit!




