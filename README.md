Postman collectionda aşağıda paylaştıklarıma benzer testler yer alıyor. <br />
Bunlar arasında özetle yer alan testler :
  * Statü kontrolü,
  * Response içerisindeki alanların kontrolü
  * Alanlara ait değerlerin kontrolü
  * Alanların tip kontrolü
  * Dizilere ait bazı kontroller (dizinin null bilgisi, dizide değer kontrolü, tip kontrolü vb.)
  * Collectiona variable ekleme
  * Collectiona variable bilgisi alma
  * Environment bilgisi ekleme ve mevcut environment bilgilerini getirme
  * Response üzerinde filtreleme işlemi
  * Birbirini takip eden farklı requestlerin ardı ardına çağırılması

```
console.log("test log");
console.warn("test warning");
console.info("test info");
console.error("test error");

pm.test("Status code is 200", function () {
    pm.response.to.have.status(200); //status 200 ise başarılı döner
});

pm.test("Successful 200 or 201", () => {
  pm.expect(pm.response.code).to.be.oneOf([200,201]); //birden fazla status kontrolü için
});

pm.test("JsonLogConsole", function () {
    var jsonData = pm.response.json();
    console.log(jsonData);
});

pm.test("JsonResponsePropertyControl", function () {
    const jsonData = pm.response.json();
    console.log(jsonData);
    pm.expect(jsonData).to.have.property('status'); //alanın olup olmadığını kontrol etmek için
});

pm.test("JsonResponsePropertyTypeControl", function () {
    const jsonData = pm.response.json();
    console.log(jsonData);
    pm.expect(jsonData.status).to.be.a('string'); //responseda yer alan status alanının tipini kontrol etmek için
    
    //Çeşitli farklı tipler için kontroller
    //pm.expect(jsonData).to.be.an("object");
    //pm.expect(jsonData.status).to.be.a("string");
    //pm.expect(jsonData.status).to.be.a("number");
    //pm.expect(jsonData.status).to.be.an("array");
    //pm.expect(jsonData.status).to.be.undefined;
    //pm.expect(jsonData.status).to.be.null; 

});

/*
//Dizilere ait kontroller
const jsonData = pm.response.json();
pm.test("TestArrayProperties", () => {
    //errors array is empty
  pm.expect(jsonData).to.be.empty;
    //areas array includes "goods"
  pm.expect(jsonData.areas).to.include("goods");
    //get the notification settings object
  const notificationSettings = jsonData.settings.find
      (m => m.type === "notification");
  pm.expect(notificationSettings)
    .to.be.an("object", "Could not find the setting");
    //detail array must include "sms"
  pm.expect(notificationSettings.detail).to.include("sms");
    //detail array must include all listed
  pm.expect(notificationSettings.detail)
    .to.have.members(["email", "sms"]);
});
*/


pm.test("Content-Type header is present", () => {
  pm.response.to.have.header("Content-Type"); //headerda herhangi bir alanın olup olmadığını kontrol etmek için
});



pm.test("JsonResponsePropertyValueControl", function () {
    const jsonData = pm.response.json();
    console.log(jsonData.status);
    console.log(jsonData["status"]);
    pm.expect(jsonData.status).to.eql("UP"); //responseda yer alan status alanının değerinin "UP" ise başarılı döner
});

pm.test("JsonResponsePropertyValueControl-2", () => {
  pm.expect(pm.response.json().status)
    .to.be.oneOf(["UP", "TEST-1", "TEST-2"]); //responseda yer alan status alanının bir liste üzerinden kontrolü
});

console.log(pm.collectionVariables.get("apiToken")); //Collectiona ait variablesları getirmek için kullanılır.
console.log(pm.collectionVariables.get("apiTokenx")); //bu isimde bir variable olmadığından undefined dönecektir.

pm.collectionVariables.set("testMyName", "Ahmet"); //Collectiona ait variables eklemek için kullanılır.
console.log(pm.collectionVariables.get("testMyName"));

//Environment Tanımları
pm.globals.set("myTestGlobalId"); //Global Environment altına myTestGlobalId isminde null değerli bir variable tanımlar.
pm.globals.set("myTestGlobalId", "testtss"); //Global Environment altına myTestGlobalId isminde bir variable tanımlayarak değer atamayı sağlar.
pm.globals.set("myTestGlobalId", ); //Global Environment altına myTestGlobalId isminde bir variable tanımlayarak değer atamayı sağlar.

const jsonData = pm.response.json();
pm.globals.set("myUpInfo", jsonData.status); //Responseda yer alan bir bilgiyi global değişken olarak tanımlayabiliriz.
//pm.globals.set("myUpInfo", jsonData[0].status); //Response bir dizi içermiş olsaydı dizinin ilk elemanı için değeri bu şekilde alabilirdik


const response = pm.response.json();
const tools = response.filter((mytool) => mytool.inStock === true); //responseda yer alan inStock alanı true olanları filreler
console.log(tools[0].id); //filrelenmiş tools içerisindeki ilk satırı loga yazdırır. Yani inStock true olan ilk kayıt yazılır.


const tool = tools[0];
console.log(tools[0].category);

if (tool) {
    console.log("--------------------------");
    pm.globals.set("toolId", tool.id);
}

pm.test("Check the active environment", () => {
  pm.expect(pm.environment.name).to.eql("TestEnvironment"); //Ortamda aktif olan environment kontrolü
});

pm.test("Tool found", () => {
    pm.expect(tool.inStock).to.eql(true); //tool.inStock alanının değeri true mu kontrolü
    pm.expect(tool.inStock).to.be.true; //tool.inStock alanının değeri true mu kontrolü
    //pm.expect(tool.category).to.eql(pm.collectionVariables.get("category")); //tool.category ile collectiondaki "category" isimli değişkenin değerini karşılaştır
    //pm.expect(tool.category).to.eql(pm.environment.get("category")); //tool.category ile environmenttaki "category" isimli değişkenin değerini karşılaştır
});

pm.test("Response Include Check", function () {
    pm.expect(pm.response.text()).to.include("GENMAX 3200 Watt Inverter Generator"); //response içerisinde bir string ifade aramak için kullanılır
});

pm.test("Response time is less than 200ms", function () {
    //bir sayının belirli bir eşik değerinden büyük olup olmadığını kontrol etmek için "above"
    //bir sayının belirli bir eşik değerinden küçük olup olmadığını kontrol etmek için "below"
    pm.expect(pm.response.responseTime).to.be.below(200); // Yanıt süresinin 200ms'den küçük olup olmadığını kontrol et
});

postman.setNextRequest("RegisterApiClient"); //collection testlerinden bundan sonra çalıştırılacak request ismi ile devam edilecek request belirlenebilir. Sonraki çalıştırılacak request ile setNextRequest belirtilen request arasında bir request var ise çalıştırılmaz.
postman.setNextRequest(null); //collection testlerinde collectiondaki requestlerin sıralamasına göre testin burada biteceğini ifade eder. Sıralamay göre yukarıdan aşağı gelerek en son burada biter. Devamında bir request varsa colleciton testlerinde çalıştırılmaz.

```
