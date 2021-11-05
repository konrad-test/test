# 1. Pliki i Katalogi

Nazwy folderów są zawsze małymi literami i jeśli występują 2 wyrazy to oddzielamy je <b>“-”</b> przykład: <b>hero-list, parcel-history.</b> <br>
Tak samo nazywamy plików: <b>hero-list.component.ts</b>, <b>parcel-history.component.ts</b> <br>

<span style="color: red">:warning: **Nie używamy camelCase przy folderach i nazwach plików** </b>

<br>

W zależności czy tworzony plik jest component, guard, service itp dodajemy mu odpowiedni suffix: 
<br>
<b>
components -> test.component.ts <br>
services -> parcel.service.ts <br>
guards -> parcel.guard.ts <br>
configs -> parcel.config.ts <br>
....
</b>
 
## Struktura katalogów:

 - app
	-pages (ja bym to zmienił na modules ? )
		- test (wszystko związane z danym modułem)
			- components
				- _partials (tutaj wrzucamy wszystkie pomocnicze komponenty np dialogi w naszym wypadku)
				- component-test
				- component-test-2
				...
				index.ts
			- configs
				test.config.ts
				...
				index.ts
			- constants
				test.constant.ts
				…
				index.ts
			- guards
				test.guard.ts
				test-list.guards.ts
				…
				index.ts
			- resolvers
				test.resolver.ts
				…
				index.ts
			- interfaces
				test.interfaces.ts
				test-list.interface.ts
				…
				index.ts
			- models
				test.model.ts
				…
				index.ts
			- pipes
				test.pipe.ts
				…
				index.ts
			- services (tutaj też staramy się rozwijać serwisy jakoś sensownie jeśli serwis jest duży)
				test.service.ts
				test-list.service.ts
				…
				index.ts
			- store
				- actions
			test.action.ts
			test-list.actiont.ts
index.ts
		- effects
			test.effect.ts
			test-list.effect.ts
			index.ts
		- reducers
			test.reducer.ts
			test-list.reducer.ts
			index.ts
		- selectors
			test.selectors.ts
			test-list.selectors.ts
			index.ts
		index.ts
			- validators
				test.validator.ts
				…
				index.ts
			test.module.ts
			test.routes.module.ts
		- test-2
			- components
				- _partials
				- component-test
				- component-test-2
			- config
			- costant 
			- guards
			- interfaces
			- models
			- services
			- store
			- validators
			test-2.module.ts
			test-2.routes.module.ts
	-shared
	 	musimy ustalić jak to tutaj będzie wyglądało

	-store (tutaj możemy dodać stora odpowiadającego za nawigację i ogólne działanie aplikacji)
		- actions
			app.action.ts
			index.ts
			router.action.ts
		- effects
			app.effect.ts
			index.ts
			router.effect.ts
		- reducers
			app.reducer.ts
			index.ts
			router.reducer.ts
		- selectors
			app.selectors.ts
			index.ts
			router.selectors.ts
		index.ts

- assets
	- fonts
	- icons
	- images
- environments
	environment.prod.ts
	environment.local.ts
	…
- scss
w tej chwili nie ma sensu myśleć o stylach chyba, że ktoś by miał czasz i chęci to może coś podziałać 



# 2. Importy
##	2.1 Skróty

Dodane zostały skróty do folderów żeby nie robić “../../../../../.. …”:

"@env/*": ["./src/environments/*"],
 "@app/*": ["./src/app/*"],
 "@config/*": ["./src/config/*"],
 "@shared/*": ["./src/app/shared/*"],
 "@style/*": ["./src/style/*"],

Przykład użycia: 

import { PaginationInterface } from '@shared/interfaces';

w katalogach dodajemy index.ts, żeby można było łatwiej importować jak przykład wyżej podajemy tylko katalog “interfaces” a nie musimy podawać konkretnego pliku.

##	2.2. Import w komponentach

	Jeśli chodzi o same importy w komponentach itp ja proponuję taką strukturę. Pierwsze importy z node_modules, linijka przerwy poniżej importy z innych modułów w tym shared -> linijka przerwy i importy z modułu.
// importy z node_module
import { Component, ViewChild } from '@angular/core';
import { FormBuilder, FormGroup } from '@angular/forms';
import { select, Store } from '@ngrx/store';
 
// importy z innych modułów w tym z shared. Używamy skrótów @app, @...
import { AuthService } from '@app/service/auth.service';
 
// importy z modułu
import { ExporterFormComponent } from './exporter-form/exporter-form.component';
import { ImporterFormComponent } from './importer-form/importer-form.component';
import { GetParcelFormComponent } from './get-parcel-form/get-parcel-form.component';
import * as fromStoreSelectors from './../../../store/selectors';
import * as fromStoreActions from './../../../store/actions';

Staramy się też używać takiej formy pamiętając, żeby się nie zapętlić :)
import * as fromStoreAction from ‘./../../store/actions’;  // Musimy ustalić kiedy używamy * 


Co do samego nazewnictwa ja proponuję robić to w ten sposób:
	- jeśli chcemy importować coś z tego samego modułu to dodajemy tylko fromInterfaces, fromStoreActions, …
	- jeśli importujemy coś z “obcego modułu” dodajemy wtedy nazwę tego modułu po from: fromUserInterfaces, fromAdminLocalInterfaces, fromUserStroreActions


	2.3. Import w modules
Fajnie nie zaśmiecać pliku modułu “test.module.ts” dlatego w pliku modul/components/index.ts zrobić coś takiego.

import { AdditionalInformationComponent } from './_partials/additional-information/additional-information.component';
import { AddParcelComponent } from './_partials/add-parcel/add-parcel.component';
 
export const components = [
   AdditionalInformationComponent,
   AddParcelComponent,
];
 
export * from './_partials/additional-information/additional-information.component';
export * from './_partials/add-parcel/add-parcel.component';

i wtedy import w module wygląda następująco 

...
import { components } from './components';
import { effects, reducers } from './store';
 
@NgModule({
 declarations: [
   EXPORT_IMPORT_MODULES,
   ...components
 ],
 imports: [
   CommonButtonsModule,
   CommonDialogsModule,
   PRIMENG,
   SharedModule,
   StoreModule.forFeature('parcels', reducers),
   EffectsModule.forFeature(effects),
 ],
 exports: [...components, EXPORT_IMPORT_MODULES],
 providers: [ConfirmationService, MessageService]
})



# 3. Store

Co do flow to najlepiej pomyśleć sobie jak byśmy chcieli żeby to działało np chcę edytować parcel, klikam edit parcel i co ma się dzieć: 
pobiera nam w efekcie wszystkie dane do edycji parcela czyli cały parcel i jakieś inne tam które są potrzebne i dopiero wtedy jest zwracany cały parcel chyba, że jakieś dane mogą poczekać. To każdy musi osobno zdecydować a najlepiej konsultować z zespołem itp :)


Przykładowę FLOW - Przykład odczytu listy parcel
w guardzie lub resolverze lub komponencie zostaje odpalona akcja odczytu listy



w reducer zmieniamy stan listy na “loading: true” i “loaded: false” 


Efekt jest również automatycznie odpalany i pobieramy dane poprzez serwis z backendu (Dane potrzebne do załadowania listy jak filtry i paginacje nie są przekazywane ale odczytywane ze stora)


jeśli pobieranie zakończyło się sukcesem odpalamy akcję z “success”



jeśli wystąpił błąd odpalamy akcję z błędem “fail” Uwaga ! Tutaj tylko odpalamy akcję ”FAIL” żadnych toastów itp Chociaż to możemy jeszcze ustalić 



odpalamy akcję fail i dopiero tutaj toast/dialog z błędem 



## 3.1. Action

Akcje, reducery, selektoryitp dzielimy anp na parcel-list, parcel itp a nie wszytko w jednym.

		

Przykładowa lista akcji


## 3.2. Effects
Efekt powinien nam dostarczyć gotowe dane które możemy zapisać do stora lub odpalić odpowiednie powiadomienie np przy error informację, że coś poszło nie tak.


## 3.3. Reducers

Nie wrzucam wszystkich akcji z reducera tylko pokazuje jak wygląda przykładowy stor parcel-list. Zauważcie, że mamy 2 pola: loaded i loading. Dzięki temu wiemy czy lista jest załadowana, czy aktualnie ładowana. Każdy taki stor powinien mieć loaded i loading żebyśmy wiedzieli w aplikacji co jest aktualnie ładowane itp


export const initialState: ParcelListState = {
 entities: { list: [], filters: fromConfigs.DEFAULT_PARCEL_LIST_FILTER, pagination: fromConfigs.DEFAULT_PARCEL_LIST_PAGINATION },
 loaded: false,
 loading: false
};
 

moglibyśmy też to rozbijać tak i nie wiem czy nie byłoby to lepsze rozwiązanie wydaje mi się czytelniejsze od tego wyżej(najlepiej teraz piszcie na teams wtedy wywiąże się dyskusja przynajmniej):

export const initialState: ParcelListState = {
 list: [],
 filters: fromConfigs.DEFAULT_PARCEL_LIST_FILTER,
 pagination: fromConfigs.DEFAULT_PARCEL_LIST_PAGINATION 
 loaded: false,
 loading: false
};



## 3.4. Selectors
Selektory służą do wyciągania danych ze stora. W selektorze możemy “wyłuskiwać” dane a nie tylko zwracać 1 do 1 czyli całego stora.

# 4. Form - budowanie form-a, mapowanie itp

	

Wrzucać pomysły odrazu z przykładową implementacją najlepiej

Jeśli chodzi o Łukasza propozycję to już tak w jednej firmie robiliśmy i jednak to średnio się sprawdzało bo nie było po co rozbijać na osobny serwis małe formy ale to jest temat do dyskusji

Moja wstępna  propozycja:
W komponencie tworzymy formularz
Mamy do dyspozycji mapToObject() // mapujemy już na gotowy obiekt który przekazujemy do effektu i później do backendu lub do komponentu rodzica który składa to w większy obiekt 
a wysyłka wyglada tak

 
   constructor(private _formBuilder: FormBuilder) {
       this.form = this._formBuilder.group({
           field1: new FormControl(''),
           field2: new FormControl(''),
           field3: new FormControl(''),
		…..
       });
   }
 


Mapowanie 

 
   private _mapToObject(): TestModel {
	… tutaj mapujemy sobie formularz na obiekt
   }


Wysyłanie 


   public updateParcel() {
       if (this.form.valid) {
           this.changeTheme.emit(this._mapToObject());
       }    }
 


# 5. Errors
Nie wiem po co nam toast.services.ts ? Bo zamist podavać do primeNg severity odpowiedni typ: succes, info, ..,. teraz odpalamy metodę showSucces, showInfo, … Rozumiem jakbyśmy tam przekazywali kod błędu a servis sam by sobie ogarniał jaki komunikat wyświetlić itp chociaż w tym przypadku to i tak chyba bez sensu bo nie mamy translate itp  

Przykład jak teraz odpalamy tosty czy to ma sens i tworzyć osobny serwis do tego jak mamy do tego już ogarnięty primeNg ? 
 showSuccess(summary: string, detail: string, timeout?: number): void {
   this.messageService.add({
     severity: 'success',
     summary,
     detail,
     life: timeout || delay
   });
 }

# 6. Pobieranie/Zapis/Update danych z backendy
	ja jestem za tym, żeby wszystko było na rest a nie teraz jakaś hybryda bo pobieranie jest z gql i rest, zapis jest do rest. Powinniśmy dążyć do restowego API i jak będzie 2 etap to chciałbym żeby wszytko było na rest api. 

Spoko jest to generowanie zapytań do resta ale to bym przedyskutował czy potrzebujemy tego. Tracimy jakiś tam wpływ na pobieranie tych danych ale z drugiej strony czy potrzebujemy jak dostajemy odpowiedni obiekt itp Do przegadania 


Pytania: 
niech każdy co ma jakieś pytania/sugestie pisze tutaj będziemy na calach/lub tutaj wyjaśniać 

Ja mam jeden temat czy oznaczamy prywatne zmienne na początku “z podłogą” (nawet w S-18 widziałem, że ktoś tak robił ) np 

public test 
private _parcel

Do ustalenia


# 7. Deployment 
Cały proces deploy-a itp z jakich branchy wychodzić to mam nadzieję, że ogarnie scrum master ale jak go nie mam to musimy sami wypracować jakiś proces 


# 8. Testy

Jakby był czas to fajnie by bylo zrobić testy jednostkowe w pierwszej kolejności a później może jakieś automatyczne przeklikiwanie itp chociaż to testerzy powinni robić bo regresję byśmy mogli “opierdolić” w takich testach 
