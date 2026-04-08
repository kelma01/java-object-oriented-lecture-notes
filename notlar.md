# Recursion

Stack recursion'un asıl gerçekleştiği yer, her call yeni bir stack frame oluşturur.

Tail recursion'da bellek daha avantajlı durumda. Geri dönüp çarpma toplama vs işlemlere girmeden son işlemden geriye rahatça dönülebilir. TCO(Tail Call Optimization) ile "Bu metot kendini çağırdıktan sonra eski veriye ihtiyacı yok." diyerek yeni bir Stack çerçevesi açmaz. Yazım ve okunma kolaylığı bakımından normal recursion daha avantajlı. Tree, Graph tarzı yapıları traverse etmek için (hem sağdan hem soldan node değerlerini çekmek gerektiğinde mesela) normal recursion daha mantıklı, aynı zamanda backtracking'i daha kolaydır.

## Örnekler

```java
public void writeVertical(int n){
    if(n<10){
        System.out.println(n);
    }
    else{
        writeVertical(n/10);
        System.out.println(n%10);
    }
}

public int factorial(int n){
    if(n == 1){
        return 1;
    }
    return n * factorial(n-1);
}

public int power(int base, int power){
    if(power == 0){
        return 1;
    }
    return base * power(base, power - 1);
}

public int linearSearch(int[] arr, int target, int low, int high){
    if(low > high){
        return -1;
    }
    int mid = (high + low)/2;
    if(target == arr[mid]){
        return mid;
    }
    else if(target < arr[mid]){
        return linearSearch(arr, target, low, mid-1);
    }
    else{
        return linearSearch(arr, target, mid+1, high);
    }
}

public int fibo(int n){
    if (n==0 || n==1){
        return n;
    }
    return fibo(n-1) + fibo(n-2);
}

//Tail Recursion
public int fibo(int n, int prev, int curr){
    if(n==0){
        return prev;
    }
    return fibo(n-1, curr, prev + curr);
}

public String reverse(String s){
    if(s.length() == 1){
        return s;
    }
    return s.charAt(s.length()-1) + reverse(s.substring(0,s.length()-1));
}

public String reverse2(String s){
    if(s.length() == 1)
        return s;
    return reverse(s.substring(1)) + s.charAt(0);
}

public int stringLength(String s){
    if(s.equals("")){
        return 0;
    }
    return 1 + stringLength(s.substring(1));
}

public int sumOfArray(int[] arr, int index){
    if(index == arr.length)
        return 0;
    return arr[index] + sumOfArray(arr, index+1);
}

// Tail Recursion
public int sumOfArray(int[] arr, int index, int sum){
    if(index==arr.length)
        return sum;
    return sumOfArray(arr, index+1, arr[index] + sum);
}
```

# Class, OOP

## Obje ve adres paylaşımlarına örnek:

### Örnek 1
```java
Person p = new Person();
p.name = "John";

Person p1 = new Person();
p1.name = "Judy";

Person p2 = new Person();
Person p3 = p;  // p3.name = "John"
Person p4 = p3; // p4.name = "John", mesela p3.name = "a" denseydi p4.name = "a" ve p.name = "John"(evet p1 de) olurdu, hepsi aynı adreste tutulduğu için value'ları paylaşımlı.
Person p5 = p2;
p = null;
p1 = null;
p2 = null;  // p2 null olsa bile p2'den türetilmiş olan p5 hala p2'deki adresi tutan p5 Person değişkeni olmaktadır.

// Kısaca kopyalama işleminde hepsi aynı adreste tutulur, value'larındaki değişme o adresi tutan değişkenleri de değiştirir. Allocated olan değişken null'a atanırsa da aynı adrestekileri null'a çekmez yalnız kendini adresten çıkarır.
```

### Örnek 2
```java
class Person implements Cloneable{
    String name;
    int age;
    Phone phone;
    Person(String name, int age, Phone phone){
        this.name = name;
        this.age = age;
        this.phone = phone;
    }
    public String toString(){
        return "Name: " + name + ", Age: " + age + ", Phone: " + phone;
    }
    protected Person clone() throws CloneNotSupportedException{
        return (Person) super.clone();
    }
}

class Phone{
    String brand;
    int model;
    Phone(String brand, int model){
        this.brand = brand;
        this.model = model;
    }
    public String toString(){
        return "Brand: " + brand + ", Phone Model: " + model;
    }
}
public class M {
    public static void main(String[] args) {
        Person p1 = new Person("Alice", 30, new Phone("Apple", 15));
        
        System.out.println("Person 1: " + p1);
        System.out.println("------------------------------");

        Person p3 = p1;
        //p3.name = "Charlie";
        p3.name = new String("Charlie");    // bu satır da üstteki satır da her türlü p1'i etkiler çünkü p3 ve p1 aynı nesneyi referans ediyorlar.
        p3.age = 35;
        p3.phone.brand = "Google";
        p3.phone.model = 10;    // bu yapılan işlem her şeyi değiştirerek p1'i de etkiler çünkü p3 ve p1 aynı nesneyi referans ediyorlar.

        System.out.println("p3 = p1 dedikten sonra:");
        System.out.println("Person 1: " + p1);
        System.out.println("Person 3: " + p3);
        System.out.println("------------------------------\n p1'e yeni bir telefon objesi atandıktan sonra durum:");
        p1.phone = new Phone("Samsung", 20);    // bu işlem p3'ü de etkiler çünkü p3 ve p1 aynı nesneyi referans ediyorlar.
        System.out.println("Person 1: " + p1);
        System.out.println("Person 3: " + p3);
        System.out.println("------------------------------");
        System.out.println("------------------------------");



        p3=null;
        p1 = new Person("Alice", 30, new Phone("Apple", 15));

        System.out.println("p3=null ve p1 eski haline atandıktan sonra vee p3=p1.clone() dedikten sonra:");
        try{
            p3 = p1.clone();
            //p3.name = "Charlie";
            p3.name = new String("Charlie");    // bu satır da üstteki satır da her türlü p1'i etkilemez çünkü p3 ve p1 farklı nesneleri referans ediyorlar.
            p3.age = 35;
            p3.phone.brand = "Google";
            p3.phone.model = 10;
            
            System.out.println("Person 1: " + p1);
            System.out.println("Person 3: " + p3);
            System.out.println("------------------------------\n p1'e yeni bir telefon objesi atandıktan sonra durum:");
            p1.phone = new Phone("Samsung", 20);    // bu işlem p3'ü etkilemez çünkü p3 ve p1 farklı nesneleri referans ediyorlar.
            System.out.println("Person 1: " + p1);
            System.out.println("Person 3: " + p3);
            System.out.println("------------------------------");
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```

Metot overload'da parametre parantezlerinin içi istenilen şekilde değişebilir. Onun dışındaki access modifier, return type, static bilgisi yine değişebilir ancak bu overload kavramı altına girmez.
Eğer bir metotun child'larda parametreleri aynıysa başka şeyleri farklıysa java bizden override etmemizi bekler, e tabi bunu yaparken de return type değiştirmemize izin vermez, access modifiers ise yalnız belirli bir ölçüde değişebilir.

Objeler kıyaslanırken == kullanılmaz, == operatörü yalnızca adreslerini karşılaştırır.

```java
new Person("ali", 2) == new Person("ali", 2);        // False döner.
new Person("ali", 2).equals(new Person("ali", 2))    // False döner.
```

## Static ve Instance initalization blokları

Static initialization block hafızaya yüklendiğinde sadece bir kere çalışır. Nesne üretilip üretilmemesi önemli değildir. Genelde sınıfa ait statik değişkenlerin atanması için ve sınıfın load'lanması için kullanılır. Program boyunca yalnızca bir kere çalışır. Sınıf load'landığı, yani kullanıldığı ilk an çalışır ve biter kısaca.

Instance initialization block ise her "new ClassName()" dendiğinde çalışır. Constructor'dan hemen önce çalışır. Amacı tüm constructor'larda ortak olan kodları tek bir yere toplamak mesela her nesneye otomatik ID atanması. Her üretilen nesne için bu işlem tekrarlanır.

### Örnek 1

```java
class A{
    static {
        System.out.println("Static blok çalıştı.");
    }

    {
        System.out.println("Instance blok çalıştı.");
    }

    A(){
        System.out.println("Constructor çalıştı.");
    }

    public static void main(String[] args){
        System.out.println("Main fonksiyon başladı.");

        A a1 = new A();
        A a2 = new A();
    }
}

/*
Output:
Static blok çalıştı.
Instance blok çalıştı.
Constructor çalıştı.
Instance blok çalıştı.
Constructor çalıştı.
*/
```

### Örnek 2

```java
class TuzakliSinif {
    // Compile-time Constant (Derleme Zamanı Sabiti)
    public final static int SIFRE = 1234; 
    
    static {
        System.out.println("🚨 TuzakliSinif Yüklendi! (Statik Blok)");
    }
}

class Baba {
    public static int babaParasi = 100;
    static { System.out.println("👴 Baba Yüklendi!"); }
    static void f(){
        System.out.println("f() Baba");
    }
}

class Cocuk extends Baba {
    public static  int cocukParasi = 10;
    public final static int cocukFinal = 1;
    static { System.out.println("👶 Cocuk Yüklendi!"); }
    static void f(){
        System.out.println("f() Cocuk");
    }
}

public class A {
    public static void main(String[] args) {
        
        // TUZAK 1: Sadece Referans Yaratmak
        // Java der ki: "Ortada nesne yok, sadece isim var. Yüklemem."
        TuzakliSinif t; 
        
        // TUZAK 2: Dizi (Array) Yaratmak
        // Java der ki: "Sadece 10 tane boş kutu açtın, içleri null. Yüklemem."
        TuzakliSinif[] kutular = new TuzakliSinif[10];
        
        // TUZAK 3: 'final static' Değişken Okumak (En Tehlikelisi!)
        // Java der ki: "Bu değer 'final' (değişmez). Ben bunu derlerken kopyalar,
        // direkt Main'in içine yazarım. Sınıfa gitmeme gerek yok. Yüklemem."
        System.out.println(TuzakliSinif.SIFRE); 
        
        // TUZAK 4.1: Çocuğun Üzerinden Babanın Malını Yemek
        // Java der ki: "Değişken aslında Baba'nın. Cocuk'a ihtiyacım yok."
        // SADECE Baba'nın statik bloğu çalışır, Cocuk YÜKLENMEZ!
        System.out.println(Cocuk.babaParasi); 

        // TUZAK 4.2:
        //baba static çalıştırır
        Baba.f();

        // TUZAK 4.3:
        // ikisinin de statik bloğu çalışır
        Cocuk.f();

        // TUZAK 4.4:
        // ikisinin de statik bloğu çalışır
        System.out.println(Cocuk.cocukParasi);

        // TUZAJ 4.5:
        // ikisinin de statik çalışmaz, baba base class da olsa yok yani.
        System.out.println(Cocuk.cocukFinal);
    }
}
```

### Örnek 3
```java
public class M {
    public static void main(String[] args) {
        Parent obj = new Child();
    }
}
class Parent {
    Parent() {
        System.out.println("Parent constructor");
    }
}
class Child extends Parent {
    {
        System.out.println("Child instance initializer");
    }
    Child() {
        System.out.println("Child constructor");
    }
}

// Öncelikle parent const, sonrasında child instance, en son child const çalıştırılır.
```

### Örnek 4
```java

class T{
    {
        System.out.println(1);
    }
    static {
        System.out.println(2);
    }
}
class M extends T{
    public M() {
        System.out.println(3);
        T t = new T();
    }
    public static void main(String [] args){
        new M();
    }
}
// 2 1 3 1
```

Eğer bir inheritance durumu mevcutsa parent static, child static, parent instance -> constructor, child instance -> constructor şeklinde ilerleyerek çalışır.

Aynı kodda birden fazla static ve instance bloklar olabilir. Java bunları yukarıdan aşağıya doğru çalıştırır.

## OOP kavramları

- Abstraction: Karmaşıklığı yönetmek için, sistemin arka planındaki detayları gizleyip kullanıcıya sadece "ne yaptığını" sunmaktır.

- Encapsulation: Veri güvenliğini ve bütünlüğünü sağlamak için, nesnenin hassas verilerini dış erişime kapatıp kontrol altına almaktır.

- Inheritance: Kod tekrarını önlemek ve yapısal bir hiyerarşi kurmak için, ortak özellikleri bir üst sınıftan alt sınıflara aktarmaktır.

- Polymorphism: Kodun esnekliğini artırmak için, farklı türdeki nesnelerin aynı komuta (metoda) kendi yapılarına uygun farklı tepkiler verebilmesini sağlamaktır.

## Access Modifiers, Encapsulation

- private: Sadece o sınıfın {} kısımları erişebilir. Child class'lar bile erişemez.
- default: Satırın başı boş bırakılırsa oluyo "int yas;" şeklinde. Sadece aynı klasör/pakettekiler görebilir.
- protected: Hem aynı pakettekiler hem de başka bir pakette olan child class'lar erişebilir.
- public: Herkese açık.

### Örnek

```java
// KLASOR p1
package example.p1;

public class A {
    public String p1_a_public = "public";
    protected String p1_a_protected = "protected";
    String p1_a_default = "default";
    private String p1_a_private = "private";
    
    public A() {
        System.out.println("A constructor");
    }
    
    public String getPrivate() {
        return p1_a_private;
    }
}
// -------------------------------------------------------------
// KLASOR p1
package example.p1;

public class B {

    void display() {
       A a = new A();
       System.out.println(a.p1_a_public);
       System.out.println(a.p1_a_protected);
       System.out.println(a.p1_a_default);
       System.out.println(a.p1_a_private);    
    }
    
}
// -------------------------------------------------------------
// KLASOR p2
package example.p2;
import example.p1.A;

public class C extends A {

    // Bu fonksiyon inherit ettiği A class'ı ile çalıştığı için public ve protected erişimlerine sahiptir.
    public void display() {
        System.out.println(p1_a_public);
        System.out.println(p1_a_protected);
        //System.out.println(p1_a_default); // error
        //System.out.println(p1_a_private); // error
    }

    // Tricky olmasının sebebi çağırılmaya çalışılan A değişkenleri inherit'ten değil, yeni oluşturulan a değişkeni ile çağırılmaya çalışılmaktadır; dolayısı ile protected erişimi de kalkmıştır hem aynı package olmaması hem de child'ı olmaması nedeni ile.
    public void displayTricky() {
        A a = new A();
        System.out.println(a.p1_a_public);
        //System.out.println(a.p1_a_protected); // error
        //System.out.println(a.p1_a_default); // error
        //System.out.println(a.p1_a_private); // error
    }


    public static void main(String[] args) {
        C c = new C();
        c.display();

        A a = new A();
        System.out.println(a.p1_a_public);
        System.out.println(a.p1_a_protected); // error
        System.out.println(a.p1_a_default); // error
        System.out.println(a.getPrivate()); 
        System.out.println(c.getPrivate()); // Inherit ettiği A class'ının getPrivate() fonksiyonunu çağırır.
    }
    
}

```

## Abstraction, Abstract Class

Nesnenin nasıl çalıştığı ile değil, ne yaptığıyla ilgilenir.

Abstract class'larda instance tutulabilir, constructor içerebilir. "new" diyerek nesnesi oluşturulamasa bile onu extend edenler super() kullanarak parent'a göre doldurabilsin diye vardır.

İki farklı şekilde metot oluşturulabilir. "abstract void f();" ile body'si olmayan şekilde oluşturulabilir, ama başına abstract gelmek zorundadır tabii ki; bu abstract fonksiyonların child'lar tarafından override edilme zorunluluğu vardır. Ya da normal şekilde fonksiyon tanımlanabilir, tabii ki child class'ların bunu override etme zorunluluğu yoktur.

Bir abstract class başka bir abstract class'ı extend ederse override edilme zorunluluğu yok, override zincirin bittiği yerde yapılmalıdır en geç senaryoda.

Doğrudan nesnesi oluşturlamaz demiştik, polymorphism ile "Arac a = new Araba()" şeklinde nesne oluşturulabilir, referans tip olarak kullanıabilir. Tabii ki böyle bir oluşturmada "a instanceof Arac" sorgusu bize true döner.

Anonymous inner class mevzusunda ise doğrudan instance'ı oluşturulabilir ancak içerisi o anda tanımlanmalıdır.(Bkz. Örnek 4)

### Örnek 1
```java
abstract class Shape{
    String color;      // İstenirse değer atanabilir ve CONSTANT olarak kullanılabilir.

    Shape(String color){
        this.color = color;
    }

    abstract double area();

    String getColor(){
        return this.color;
    }
}

class Circle extends Shape{
    int radius;

    Circle(String color, int radius){
        //Mesela super(color) dediğimizde parent'ın color'una gitmiş olduk, eğer circle'da da color olsaydı normal color onu çağırırdı. Metotlar da super ile parent'taki fonksiyonu çağırabilir. Parent'ta a() olsun, bu class'ta a() olsaydı ve super.a(); deseydik önce parent'ı çalıştır sonra normal class'taki a()'yı çalıştır durumu olurdu. super() ilk satırda bulunmalıdır. Çoklu interface implement edilseydi ve birinin super'ini çağırmak isteseydik ParentClassA.super.func(); şeklinde çağırırdık. super()'in içini parent class'taki constructor yapısı ile doldurulmalıdır, kısaca aşağıdaki super() içine color yazılmak zorundayızdır, Shape'te boş constructor olsa onu super() ile çağırabilirdik tabii ki.
        super(color);   
        this.radius = radius;
    }

    @Override
    double area(){
        return 3.14 * radius * radius;
    }
}

//Main'de çağırırken
Shape s = new Color("Red", 5);
s.getColor();
s.area();
```

### Örnek 2
```java
abstract class Arac {
    // 1. Gövdesiz Metot (Zorunlu Görev)
    // abstract kelimesi ŞARTTIR, overeride edilmeli en sonunda bi yerde.
    abstract void yakitAl(); 

    // 2. Gövdeli Metot (Hazır Fonksiyon)
    // Çocuğun bunu yazmasına gerek yok, direkt kullanır.
    void calistir() {
        System.out.println("Motor çalıştı.");
    }
}
class Araba extends Arac {
    // yakitAl() abstract olduğu için MECBUR Override ettim
    @Override
    void yakitAl() {
        System.out.println("Benzin alınıyor...");
    }
    
    // calistir() metoduna dokunmadım, babadakini kullanırım.
}
```

### Örnek 3
```java
abstract class Canli {
    abstract void nefesAl();
}

// Memeli de Abstract, o yüzden nefesAl()'ı doldurmak zorunda değil.
// Hatta kendi derdini (dogur) de ekleyip paslıyor.
abstract class Memeli extends Canli {
    abstract void dogur();
}

// İhale Kedi'ye kaldı. Hepsini yapmak zorunda.
class Kedi extends Memeli {
    @Override
    void nefesAl() { System.out.println("Nefes alıyor"); }
    
    @Override
    void dogur() { System.out.println("Doğuruyor"); }
}
```

### Örnek 4
```java
abstract class Boya {
    abstract void boyaYap();
}

public class Main {
    public static void main(String[] args) {
        // Hileli yöntem: Anonymous Class
        Boya b = new Boya() {
            @Override
            void boyaYap() {
                System.out.println("Şipşak boya yapıldı!");
            }
        };
        
        b.boyaYap();
    }
}
```

### Örnek 5
```java
abstract class A{
    abstract void one();
    void two(){
        System.out.println(2);
        one();
    }
}
abstract class B extends A{
    void one(){
        System.out.println(1);
        three();
    }
}
class C extends B{
    void three(){
        System.out.println(3);
    }
}
class Main{
    public static void main(String[] args){
        C c = new C();
        c.one();
        c.two();
        c.three();
    }
}
//Output: 132133
```

### Örnek 6
```java
new Y().method(100) // ile çağırıldığında 100 integer olarak yorumlanır. method(double n) ve method(int n) arasından int n olanı tercih eder yani.
```

### Örnek 7
```java
abstract class BabaSinif {
    static void kuraliOku() {
        System.out.println("Baba kuralları okuyor.");
    }
}

class CocukSinif extends BabaSinif { }

public class A {
    public static void main(String[] args) {
        // 1. Doğru ve temiz yol:
        BabaSinif.kuraliOku(); 
        
        // 2. Alt sınıf üzerinden (Çalışır, uyarı verir):
        CocukSinif.kuraliOku();

        // 3. Nesne üzerinden (Çalışır, uyarı verir):
        CocukSinif c = new CocukSinif();
        c.kuraliOku();

        // 🚨 4. MÜLAKAT SORUSU (Sence bu patlar mı?):
        CocukSinif bosNesne = null;
        bosNesne.kuraliOku(); // ÇALIŞIR! NullPointerException VERMEZ! Çünkü metot her türlü statik, instance'ının nası olduğuna bakmadan sınıf üzerinden çağırılmaktadır.
    }
}
```

## super() Kullanım Örnekleri

### Örnek 1
```java
class Baba {
    String isim = "Ahmet (Baba)";
}

class Cocuk extends Baba {
    String isim = "Mehmet (Cocuk)";

    void isimleriGoster() {
        // Varsayılan olarak (this) mevcut sınıfın değişkenini alır
        System.out.println("Varsayılan: " + isim);       // Çıktı: Mehmet (Cocuk)
        
        // this keyword'ü ile açıkça belirtilirse yine mevcut sınıfı alır
        System.out.println("this ile: " + this.isim);    // Çıktı: Mehmet (Cocuk)
        
        // super keyword'ü ile ÜST SINIFIN değişkenine erişilir
        System.out.println("super ile: " + super.isim);  // Çıktı: Ahmet (Baba)
    }
}
```

### Örnek 2
```java
class VergiHesaplayici {
    void hesapla() {
        System.out.println("Temel KDV (%18) hesaplandı.");
    }
}

class LuksVergiHesaplayici extends VergiHesaplayici {
    @Override
    void hesapla() {
        // 1. Önce babanın metodunu çağır (Kod tekrarını önler)
        super.hesapla(); // Direkt olarak super() ile çağırılamaz.
        
        // 2. Sonra üzerine kendi eklemelerini yap
        System.out.println("Lüks Tüketim Vergisi (ÖTV) eklendi.");
    }
}
class HatalıOrnek extends VergiHesaplayici {
    
    void rastgeleMetot() {
        // --- HATALI KULLANIM ---
        // super(); 
        // HATA: "Call to super must be first statement in constructor"
        // Nesne zaten oluştuğu için tekrar kurucu çağrılamaz.
        
        // --- DOĞRU KULLANIM ---
        super.hesapla(); // Babanın metodu çağrılabilir (Parantez yok, nokta var).
    }
}
```

### Örnek 3
```java
// Dipnot: Java daima derived constructor'a super() komudunu ekler, çalışırsa zaten sıkıntı yok ama çalışmayacağı/error atacağı bir durumda bunu manuel olarak handle etmek gerekir.
class Personel {
    String ad;
    
    // Boş constructor YOK! Sadece parametreli var.
    public Personel(String ad) {
        this.ad = ad;
        System.out.println("Personel oluşturuldu: " + ad);
    }
}

class Muhendis extends Personel {
    public Muhendis() {
        // HATA: super(); // Java buraya otomatik boş super() ekleyemez çünkü babada yok.
        
        // DOĞRU: Babayı parametre ile beslemek zorundayız.
        super("İsimsiz Mühendis"); 
        System.out.println("Mühendis sınıfı hazır.");
    }
    public Muhendis(String ad){
        // HATA: super(); // ad parametresi alsa bile arkada çalışacak olan budur yine hata atacaktır.

        super(ad); // Bunu koyarak error'u kaldırmış oluruz. Java artık kendi super()'ini çağırmaz, bunu kullanır.
    }
}
```

### Örnek 4
```java
public class Employee {
    String name;
    double salary;
    Employee manager;

    // 1. Temel Constructor
    public Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }

    // 2. Genişletilmiş Constructor
    public Employee(String name, double salary, Employee manager) {
        // YANLIŞ: super(name, salary); // Üst sınıfta (Object) böyle bir constructor yok!
        // YANLIŞ: super() yazmayıp boş bıraksan da yine super() çağırılır, ancak yine name ve salary null ve 0 atalı olur yine de.
        
        // DOĞRU: Bu sınıfın diğer constructor'ına devret. Her türlü this kullanılarak önceki constructor'lar kullanılır.
        this(name, salary);  
        
        // Geriye kalan işi burada hallet.
        this.manager = manager;
    }
}
```

### Örnek 5
```java
class Baba {
    String ruzgar = "Poyraz (Baba)";
    
    void es() {
        // Buradaki 'this' çalışma zamanındaki nesneyi (Cocuk) gösterse bile,
        // DEĞİŞKENLERDE polymorphism yoktur!
        // Kod 'Baba' sınıfında yazıldığı için, 'ruzgar' her zaman Baba'nınkidir.
        System.out.println("Baba Esti: " + this.ruzgar);
    }
}

class Cocuk extends Baba {
    String ruzgar = "Meltem (Cocuk)"; // Variable Hiding (Shadowing değil!)

    @Override
    void es() {
        System.out.println("Çocuk Esti(this.ruzgar): " + this.ruzgar);   // ÇOK ÖNEMLİ. Cocuk Baba'dan türetilse bile buradaki this.ruzgar Çocuk'takini çağırır, dümdüz ruzgar diyerek çağırsaydı Baba'yı çağırırdı.
        System.out.println("Çocuk Esti(ruzgar): " + ruzgar);   // ÇOK ÖNEMLİ. Cocuk Baba'dan türetilse bile buradaki this.ruzgar yine Çocuk'takini çağırır.
    }
}

public class A {
    public static void main(String[] args) {
        Baba b = new Cocuk();
        
        // Metot Polymorphic'tir -> Çocuğun es() metodu çalışır.
        b.es(); 
        // Çıktı: "Çocuk Esti: Meltem (Cocuk)"
        
        // AMA ŞİMDİ DİKKAT!
        // Referans tipi 'Baba' olduğu için, değişkene doğrudan erişirsen Babanınkini alırsın.
        System.out.println("Doğrudan Erişim: " + b.ruzgar); //this.ruzgar denilseydi Cocuk class'ındaki çağırılırdı. 
        // Çıktı: "Poyraz (Baba)"  <-- ŞOK!
    }
}
```

### Örnek 6
```java
class A {
    void f(int a) {
        System.out.println("A sınıfı: " + a);
    }
}

class B extends A {
    @Override // Bu etiketi yazmak hayat kurtarır!
    void f(int a) {
        super.f(a); // Babadaki f(int) çalışsın
        System.out.println("B sınıfı ekstra işlemler...");
    }
}

class C extends A {
    // DİKKAT: Bu bir OVERRIDE (Ezme) değil, OVERLOAD'dur (Aşırı yükleme).
    // Çünkü imza (parametreler) değişti.
    void f(int a, int b) {
        super.f(a); // Babanın metodunu kullanabilirsin
        System.out.println("C sınıfı overload: " + a + " ve " + b);
    }
}
```

## Inheritance, Polymorphism Örnekleri

### Örnek 1
```java
class A {
    int a = 0;
    final int b = 0;

    static {
        System.out.println("A Static Block");
    }
    {
        System.out.println("A Initialization Block");
    }

    public A(){
        System.out.println("A Constructor");
    }
    public static void display() {
        System.out.println("Static A display");
    }
    public void display2() {
        System.out.println("Non-static A display");
    }
    //final method child class'ta override edilemez
    public final void display3() {
        System.out.println("Final A display");
    }
}

class B extends A {
    int a = 1;
    final int b = 1;

    static {
        System.out.println("B Static Block");
    }
    {
        System.out.println("B Initialization Block");
    }

    B() {
        System.out.println("B Constructor");
    }
    //override değil çünkü static method.
    public static void display() {
        System.out.println("Static B display");
    }
    @Override
    public void display2() {
        System.out.println("Non-static B display");
    }

}

public class M{
    public static void main(String[] args) {
        A.display();    //static blok ve static fonksiyonu çağırır
        B.display();    //static blok ve static fonksiyonu çağırır
        A b_a = new B();    //A initialization, A constructor, B initialization B constructor çağırır.
        System.out.println("----");

        B b_b = (B) b_a;    // B'ye dönüştürür, constructor veya initialization blok çağırmaz çünkü nesne zaten oluşturulmuş
        b_a.display();  // Static metodu A sınıfından çağırır (bildirilen tipe göre)
        b_b.display();  // Static metodu B sınıfından çağırır (bildirilen tipe göre)
        b_a.display2(); // Non-static metodu B sınıfından çağırır (gerçek nesne tipi)
        b_b.display2(); // Non-static metodu B sınıfından çağırır (gerçek nesne tipi)

        System.out.println(b_a.a);  //A sınıfından türetildiği için A sınıfının a'sını alır
        System.out.println(b_b.a);  //B sınıfından türetildiği için B sınıfının a'sını alır
        System.out.println(b_a.b);  //A sınıfından türetildiği için A sınıfının b'sini alır
        System.out.println(b_b.b);  //B sınıfından türetildiği için B sınıfının b'sini alır
    }
    // KISACA: STATİK METOT ÇAĞIRILIYOR ISE BİLDİRİLEN TİPE GÖRE, NORMAL METOT İSE OVERRIDE ZINCIRINE GÖRE ÇAĞIRILIR. YANİ STATIC METOTLARDA POLYMORPHISM YOK DENEBİLİR.
    // KISACA: DEĞİŞKENLER OVERRIDE EDİLMEZ, BİLDİRLEN TİP NEYSE ONUN CİNSİNDEKİ DEĞİŞKENLERİ BASTIRIR.
}
```

### Örnek 2
```java
class Vehicle {
    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    protected void start() {
        System.out.println(brand + " is starting.");
    }

    public void stop() {
        System.out.println(brand + " is stopping.");
    }
}

class Car extends Vehicle {
    private int numDoors;

    public Car(String brand, int numDoors) {
        super(brand);
        this.numDoors = numDoors;
    }

    public void start() {
        System.out.println("Car " + brand + " is starting.");
    }

    public void drive() {
        System.out.println(brand + " is driving with " + numDoors + " doors.");
    }
}

class Bike extends Vehicle {
    private boolean hasBasket;

    public Bike(String brand, boolean hasBasket) {
        super(brand);
        this.hasBasket = hasBasket;
    }
 
    public void start() {
        System.out.println("Bike " + brand + " is starting.");
    }

    public void ride() {
        System.out.println(brand + " is riding" + (hasBasket ? " with a basket." : "."));
    }
}

public class A {
    public static void main(String[] args) {
        // Upcasting
        Vehicle car = new Car("Toyota", 4);
        Vehicle bike = new Bike("Honda", true);

        // Polymorphic behavior, çağırılan start metodu Vehicle türüne göre değil, gerçek nesne türüne göre çalışır. Çünkü metodlar override edilmiştir.
        car.start();
        bike.start();

        // Downcasting
        // Downcast edilmeseydi drive() ve ride() metodları çağrılamazdı, çünkü Vehicle sınıfında bu metodlar yok. Ana tür Vehicle olarak kalırsa sadece Vehicle sınıfındaki metodlar erişilebilir olurdu.
        // Downcast ederken direkt cast edilmez, öncesinde instanceof kontrolünün yapılması zorunlu yoksa runtime error atabilir.
        if (car instanceof Car) {
            Car specificCar = (Car) car;
            specificCar.drive();    // Çıktı: "Toyota is driving with 4 doors.". Çünkü car nesnesi Car türünde ve drive() metodu Car sınıfında override edilmiştir.
        }

        if (bike instanceof Bike) {
            Bike specificBike = (Bike) bike;
            specificBike.ride();    // Çıktı: "Honda is riding with a basket.". Çünkü bike nesnesi Bike türünde ve ride() metodu Bike sınıfında override edilmiştir.
        }

        System.out.println("\n\nArray example:");
        
        Vehicle[] vehicles = new Vehicle[3];
        vehicles[0] = new Car("Ford", 2);
        vehicles[1] = new Bike("Yamaha", false);
        vehicles[2] = new Car("Chevrolet", 4);

        for (Vehicle vehicle : vehicles) {
            vehicle.start();    // Buradaki start() metodu polymorphic olarak çalışır, çağırdığı start ise gerçek nesne türüne göre belirlenir.
            if (vehicle instanceof Car) {
                ((Car) vehicle).drive();
            } else if (vehicle instanceof Bike) {
                ((Bike) vehicle).ride();
            }
            vehicle.stop();    // stop() metodu Vehicle sınıfında tanımlı, bu yüzden doğrudan çağrılabilir. Eğer override edilmediyse Vehicle sınıfındaki stop() metodu çalışır, edilseydi gerçek nesne türüne göre çalışırdı.
            System.out.println();
        }
    }
}
```

### Örnek 3
```java
class A{}
class B extends A{}
class C extends B{}
public class Main{
    static void f(A a){
        System.out.println(1);
    }
    static void f(B b){
        System.out.println(2);
    }
    static void f(Object obj){
        System.out.println(3);
    }
    public static void main(String[] args){
        C c = new C();
        f(c);   // Çıktı olarak 2 verir, C parametre alan fonksiyon yok öyleyse parent'ına bakılır o da B class'ı. B'yi parametre alan fonksiyon var, öyleyse çıktı olarak 2 verilir. 
    }
}

// ----------------- BİR DİĞER VERSİYONU AŞAĞIDA -------------------

class A{}
class B extends A{}
class C {}
public class Main{
    static void f(A a){
        System.out.println(1);
    }
    static void f(B b){
        System.out.println(2);
    }
    static void f(Object obj){
        System.out.println(3);
    }
    public static void main(String[] args){
        C c = new C();
        f(c);   // Çıktı olarak 3 verir, C parametre alan fonksiyon yok öyleyse parent'ına bakılır o da yok. Öyleyse Object parametresi olana bakılır, o da 3 çıktısını verecek olan fonksiyon.
    }
}
```

### Örnek 4
```java
class X{
    void f(){
        System.out.println(1);
    }
}
class Y extends X{
    @Override
    void f(){
        System.out.println(2);
    }
}
class Z extends Y{
    @Override
    void f(){
        System.out.println(3);
    }
}
public class Main{
    public static void main(String[] args){
        X x = new Y();  // Bir Y nesnesi oluşturulur new ile.
        x.f();  // 2 çıktısını verir, çünkü override edilmiş olan f() çağırılır.
        Y y = (Y) x;
        y.f();  // Yine 2 çıktısını verir, x downcast edilmiştir ve y bir Y nesnesidir.
        Z z = (Z) y;    //Runtime error atar: "Y cannot be cast to Z" şeklinde. Bunun sebebi ilk Y'nin new ile tanımlanmış olmasıdır. İlk downcast'te hata atmamasının sebebi X üzerinden türetilmesidir. X-Y olan bir objeden Y-Y şeklinde downcast edebildik ama sıra Z'ye geldiğinde Y-Y olan bir objeyi Z-Z olan bir objeye cast etmeye çalıştık ve hatayı burada aldık.
        z.f();  
    }
}
```

### Örnek 5
```java
class Shape {  
    public String name;      
    final static int pi = 2;
    public Shape() {  
        name = "Generic";  
        printName(); 
    }  
    public void printName() {  
        System.out.println("Shape class printName(name): " + name);  
    }
}  

class Circle extends Shape {  
    private double radius;
    public Circle(double r) {  
        radius = r;  
        name = "Circle";
    }  
    public double getArea() {  
        return pi * radius * radius;  
    }
    public void printName(){
        System.out.println("Circle class printName(name): " + name);  
    }
} 
class Main {
    public static void main(String[] args) {
        Circle c = new Circle(2.0); // "Circle class printName(name): Generic" çıktısı alınır. Önce Shape'in constructor'ı çalıştırılır.
        c.printName();  // Circle
        System.out.println(c.getArea());    // 8
    }  
}
```

### Örnek 6 - yvşak bir örnek
```java
class Base {
    int x = 10;
    Base() { 
        print(); 
    }
    void print() { 
        System.out.print("Base"); 
    }
}
class Derived extends Base {
    int x = 1;
    void print() { 
        System.out.print(x); 
    }
}
class Main {
    public static void main(String[] args) {
        new Derived();
    }
}

/**
 * Çıktı 0 gelir. Yeni Derived() oluşturulduğu zaman super() ile Base constrcutor çağırılır. Base sınıfında print() çağırılmaz çünkü subclass Derived'ta print() override edilmiş ve o Derived'in print()'i çağırılır. Bu print()'teki çağırılacak olan x değişkeni Derived'ta tanımlannmış olan x=1'dir. Ancak hala Base constructor'ı içindeyiz, dolayısı ile Derived'taki 1 hala atanmadı ve x'in değeri bulunmamakta. Dolayısı ile o x'in default değeri olan 0 çıktısı görülür.
*/
```

### Örnek 6
```java
class Parent {
    void display() {
        System.out.println("Parent display");
    }
}
class Child extends Parent {
    void display() {
        System.out.println("Child display");
    }
    void special() {
        System.out.println("Child special");
    }
}
public class Demo {
    public static void main(String[] args) {
        Parent p = new Child();
        p.display();
        p.special();
    }
}

/**
 * Bu kod öncelikle çalışmaz, çünkü p.special() çağırılmaya çalışıldığında Parent içerisinde special fonksiyonu olmadığı için hatalıdır. 
 * Eğer p objesi Child p = new Child(); diye oluşturulsaydı special() çalışırdı çünkü tek başına bir obje o artık. 
 * p.special'i silip yalnız p.display() çağırıldığında ise Child display mesajını alırız.
*/
```

### Örnek 7
```java
class A{
    int a=1;
}
class B extends A{
    int a=5;
    int c=3;
}
class Main{
    public static void main(String[] args) {
        A obj1 = new A();
        A obj2 = new B();
        B obj3 = new B();

        obj1.a; // 1 gelir.
        obj2.a; // 1 gelir, A'da türetildiği için ve değişkenler override edilmediği için.
        obj2.c; // Çalışmaz, çünkü A'dan türetildiği için ve A sınıfında c değişkeni tutulmadığı için c'nin burada ne olduğu bilinemez. Aynı bir üst örnekte special()'in çalışmaması gibi.
        obj3.a; // 5 gelir. B'den türetildiği için A'da tanımlanmış olan umursanmaz.
        obj3.c; // 3 gelir.
    }
}
```

### Örnek 8
```java
class A{
    protected void f(){

    }
}
class B extends A{
    /*
    private void f(){
        BU OLMAZ. BASE CLASS PROTECTED KOYMUŞ, ONU DERIVED EDEN SINIF DAHA DA ÖZELLEŞTİREMEZ DAHA DA GENİŞLETEBİLİR ANCAK. 
        BU KURAL AYNI ŞEKİLDE ABSTRACT'TAN OVVERIDE EDİLİRKEN VE ANONYNOUS CLASS'TA DA GEÇERLİ.
        --> VISIBILITY REDUCE EDİLEMEZ...
    }
    */
    public void f(){

    }
}
```


## Cloning

### Shallow Copy

object.clone() ile yapılan klonlama işlemi. Nesnenin bir kopyasını oluşturur, değerleri kopyalar ancak adresler aynı yere atanır. Yani b.clone(a) diyip devamında a.age = 10 dersek b.age value'si de 10 değerini alır çünkü aynı adreste bulunmaktalar. Buna shallow copy de denir.

#### Örnek 1
```java
// Cloneable interface'ini implemente etmelisin (Yoksa hata verir)
class Motor {
    int guc;
    public Motor(int guc) { 
        this.guc = guc; 
    }
}

class Araba implements Cloneable {
    String marka;
    Motor motor;

    public Araba(String marka, int guc) {
        this.marka = marka;
        this.motor = new Motor(guc);
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); // Shallow Copy
    }
}

public class Main {
    public static void main(String[] args) throws Exception {
        Araba araba1 = new Araba("BMW", 601);
        Araba araba2 = (Araba) araba1.clone(); // Kopyaladık sanıyoruz...

        araba2.motor.guc = 500;
        System.out.println(araba1.motor.guc); // Çıktı: 500 (601 olmalıydı)
    }
}
```


#### Örnek 2
```java
class Ex {
    private int[] data;
    
    // Shallow copy
    public Ex(int[] values) {
        this.data = values;
    }
    public void showData() {
        System.out.println(Arrays.toString(data));
    }
}
class UsesEx {
    public static void main(String[] args) {
        int[] vals = {3, 7, 9};
        Ex e = new Ex(vals);

        e.showData();   // [3, 7, 9]

        vals[0] = 13;
        e.showData();   // [13, 7, 9]   Demek ki array'leri de direkt kopyalansın diye verince shallow copy oluyormuş
    }
}
```

### Deep Copy

Otomatik olarak yapılmaz, self-code gerekir. Amaç tam bağımsızlığın sağlanmasıdır. Yukarıdaki örnekteki Ex class'ı aşağıdaki gibi yazılırsa problem ortadan kalkmış olur:

```java
class Ex {
    private int[] data;

    // Deep copy örneği, tek tek değerler assign edilmektedir.
    public Ex(int[] values) {
        data = new int[values.length];
        for (int i = 0; i < values.length; i++) {
            data[i] = values[i];
        }
    }
    public void showData() {
        System.out.println(Arrays.toString(data));
    }
}
```

### Lazy Copy

Bu ise kopyalanma işleminin constructor'da yapılmasındansa constructor kısmında sadece array'i initialize edip, value'ları ise setValue() fonksiyonu ile doldurma işlemidir.

## Interface

Interface, bir sınıfın nasıl yapacağını söyleyen şablondur. Implement edildiğinde içerisindeki tüm metotları yerine getirmek, kullanmak zorundadır. 

Multi implements yapılabilir. Constructor'u bulunmaz ve instance'ı oluşturulamaz. Interface içerisinde tanımlanacak olan instance'lar da "public final static" olarak tanımlanır(yazılmasa bile implicit olarak bu şekilde tanımlanır). Yani aslında değişken olarak değil bir constant olarak kullanılır, mesela "public final static RATE_LIMIT = 5;" gibi. Dışarıdan onu implement eden class'lar onun objesini oluşturabilir(Örnek 1'de GPS implement eden MyPhone gibi.).

Interface'ler birbirlerini extends ile inherit ederler ve parent'ındakileri override etmek zorunda kalmaz. Interface içerisindeki tüm metotlar onu implement eden sınıflar tarafından override edilmelidir ve hepsi public olmak zorundadır(alt paragraftaki bilgi önemli!). Interface'i implement eden sınıf abstract bir sınıf ise override etme zorunluluğu yoktur normal sınıflarda olduğu gibi. Gerisini o abstract sınıfı extends edecek sınıf halleder, kısaca override işlemi zincirin en sonunda olmalıdır. Eğer interface içerisinde metot body'si doldurulmuş ise override edilme gibi bi zorunluluk taşımaz öylece kullanılabilir.

Interface'in static metodu çocuğa geçmez!Child.ParentStaticMethod() hata atar.!

Interface metodunu interface extend edip override edecekse override edilen fonksiyon default, class implement edip override edecekse override edilen fonksiyon public olmalı.

### Örnek 0
```java
interface a{
    void m1();
}
interface b extends a{
    // override edilme zorunlu değil
    // edilseydi ama şöyle olurdu:
    /*default void m1(){
        System.out.println("m1");
    }*/
}

abstract class c implements b{
    // override edilme zorunlu değil
    // ama edilseydi ama şöyle olurdu:
    /*public void m1(){
        System.out.println("m1");
    }*/
}
class d extends c{
    public void m1(){
        System.out.println("m1");
    }

    // eğer yukarıda override edilseydi, burada override edilmek zorunda olmazdı ve aşağıdaki obj.m1() yine de "m1" yazdırırdı.
}
class M{
    public static void main(String[] args) {
        a obj = new d();
        obj.m1();
    }
}
```

### Örnek 1
```java
// Interface representing GPS functionality
interface GPS {
    void enableGPS();

    void disableGPS();

    void getCurrentLocation();
}

// Interface representing Radio functionality
interface Radio {
    void turnOnRadio();

    void turnOffRadio();

    void tuneRadioStation(String station);
}

// Interface representing Camera functionality
interface Camera {
    void capturePhoto();

    void recordVideo();
}

// MobilePhone class implementing GPS, Radio, and Camera interfaces
class MobilePhone implements GPS, Radio, Camera {
    private String model;
    private String manufacturer;

    public MobilePhone(String model, String manufacturer) {
        this.model = model;
        this.manufacturer = manufacturer;
    }

    public String getModel() {
        return model;
    }

    public String getManufacturer() {
        return manufacturer;
    }

    // Implementing GPS interface methods
    public void enableGPS() {
        System.out.println("GPS is enabled on " + getManufacturer() + " " + getModel());
    }

    public void disableGPS() {
        System.out.println("GPS is disabled on " + getManufacturer() + " " + getModel());
    }

    public void getCurrentLocation() {
        System.out.println("Fetching current location on " + getManufacturer() + " " + getModel());
    }

    // Implementing Radio interface methods
    public void turnOnRadio() {
        System.out.println("Radio is turned on on " + getManufacturer() + " " + getModel());
    }

    public void turnOffRadio() {
        System.out.println("Radio is turned off on " + getManufacturer() + " " + getModel());
    }

    public void tuneRadioStation(String station) {
        System.out.println("Tuning to radio station " + station + " on " + getManufacturer() + " " + getModel());
    }

    // Implementing Camera interface methods
    public void capturePhoto() {
        System.out.println("Capturing a photo on " + getManufacturer() + " " + getModel());
    }

    public void recordVideo() {
        System.out.println("Recording a video on " + getManufacturer() + " " + getModel());
    }
}

class NavigationSystem implements GPS {

    private final String model;

    public NavigationSystem() {
        this.model = "Default Model";
    }

    public NavigationSystem(String model) {
        this.model = model;
    }

    public String getModel() {
        return model;
    }
    public void enableGPS() {
        System.out.println("GPS is enabled on the navigation system");
    }

    public void disableGPS() {
        System.out.println("GPS is disabled on the navigation system");
    }

    public void getCurrentLocation() {
        System.out.println("Fetching current location on the navigation system");
    }

    public void navigateTo(String destination) {
        System.out.println("Navigating to " + destination + " on the navigation system");
    }
}
public class Main {
    public static void main(String[] args) {
        // Creating a mobile phone instance
        MobilePhone myPhone = new MobilePhone("Galaxy S21", "Samsung");

        // Using GPS functionality
        myPhone.enableGPS();
        myPhone.getCurrentLocation();
        myPhone.disableGPS();

        System.out.println();

        // Using Radio functionality
        myPhone.turnOnRadio();
        myPhone.tuneRadioStation("FM 101.5");
        myPhone.turnOffRadio();

        System.out.println();

        // Using Camera functionality
        myPhone.capturePhoto();
        myPhone.recordVideo();

        System.out.println();

        NavigationSystem teslaNavigator = new NavigationSystem("Tesla Model 3");
        teslaNavigator.enableGPS();
        teslaNavigator.getCurrentLocation();
        teslaNavigator.navigateTo("Home");


        // Burada GPS sistem implement edildiği için onun adına oluşturulmuş sınıflar array'ine eklenebilir. Upcasting durumuna örnektir ve polymorphism gösterir.
        System.out.println();
        System.out.println("Testing GPS Systems");
        GPS[] gpsDevices = new GPS[2];
        gpsDevices[0] = myPhone;
        gpsDevices[1] = teslaNavigator;
        // Aynı şekilde şu da yapılabilir:
        /*
        GPS gpsPhone = myPhone;
        GPS gpsNavigator = teslaNavigator;
        gpsPhone.enableGPS(); // Çağırdığı method MobilePhone sınıfına aittir.
        gpsNavigator.enableGPS(); // Çağırdığı method NavigationSystem sınıfına aitt
        */

        for (GPS gpsDevice : gpsDevices) {
            gpsDevice.getCurrentLocation();
        }

        System.out.println(myPhone instanceof GPS); // True döner, onu implement ettiği için.
        // myPhone Object olarak tanımlanmsa bile GPS interface'ini implement ettiği için instanceof GPS true dönecektir.
        // Tek istisna, MobilePhone phone = null; şeklinde tanımlanırsa instanceof GPS false dönecektir çünkü null referans herhangi bir şeye işaret etmez.
    }
}
```

### Örnek 1'e küçük bir örnek daha
```java
interface InterfaceA {
    int a = 3;

    void f();
}

class ClassA implements InterfaceA {
    int a = 4;

    @Override
    public void f() {
        System.out.println("f");
    }
    public void g() {
        System.out.println("g");
    }
}

public class A{
    public static void main(String[] args) {
        InterfaceA a = new ClassA();
        ClassA b = new ClassA();
        
        System.out.println(a.a); // 3
        System.out.println(b.a); // 4
            
        a.f();
        a.g(); // This will cause a compile-time error because g() is not defined in InterfaceA
        b.f();
        b.g();
    }
}
```

### Örnek 1'e bir küçük örnek daha
```java
interface InterfaceA{
    int CONST = 10;
    void f();
}
class ClassA implements InterfaceA{
    int CONST = 20;
}
public class M {
    public static void main(String[] args) {
        ClassA obj = new ClassA();
        System.out.println(obj.CONST); // Output: 20
        System.out.println(InterfaceA.CONST); // Output: 10

        InterfaceA obj2 = new ClassA();
        System.out.println(obj2.CONST); // Output: 10


        new ClassA(){
            public void f(){
                System.out.println("Hello from anonymous class!");
                System.out.println(CONST); // Output: 20
                System.out.println(InterfaceA.CONST); // Output: 10
            }
        }.f();
    }
}
```

### Örnek 2
```java
package p7;

// Interface representing items that can be used
interface Usable {
    void use();
}

// Interface representing consumable items
interface Consumable {
    void consume();
}

// Interface representing equippable items
interface Equippable {
    void equip();
}

// Base class representing a game item
class GameItem implements Usable {
    private String name;

    public GameItem(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }

    @Override
    public void use() {
        System.out.println("Using " + name);
    }
}

// Derived class representing a health potion
class HealthPotion extends GameItem implements Consumable {
    private int healingPower;

    public HealthPotion(String name, int healingPower) {
        super(name);
        this.healingPower = healingPower;
    }

    @Override
    public void use() {
        System.out.println("Using " + getName() + " to restore " + healingPower + " health points.");
    }

    @Override
    public void consume() {
        System.out.println("Consuming " + getName() + " for healing.");
    }
}

// Derived class representing a weapon
class Weapon extends GameItem implements Equippable {
    private int damage;

    public Weapon(String name, int damage) {
        super(name);
        this.damage = damage;
    }

    @Override
    public void use() {
        System.out.println("Equipping " + getName() + " with damage " + damage + ".");
    }

    @Override
    public void equip() {
        System.out.println("Equipping " + getName() + " as a weapon.");
    }
}

public class Main {
    public static void main(String[] args) {
        // Creating an array of upcast objects representing a player's inventory
        Usable[] inventory = new Usable[4];
        inventory[0] = new HealthPotion("Health Elixir", 30);
        inventory[1] = new Weapon("Sword of Fire", 50);
        inventory[2] = new Weapon("Bow of Agility", 40);
        inventory[3] = new HealthPotion("Healing Salve", 20);

        // Player using and consuming items in the inventory
        for (Usable item : inventory) {
            item.use();

            // Check if the item is consumable and consume it
            if (item instanceof Consumable) {
                ((Consumable) item).consume();
            }

            // Check if the item is equippable and equip it
            if (item instanceof Equippable) {
                ((Equippable) item).equip();
            }

            System.out.println();
        }
    }
}
```

### Örnek 3
```java
// Tanımdan dendiği gibi doğrudan instance'ı oluşturulamaz ama aşağıdaki şekilde tek kullanımlık bir class üretmek için kullanılabilir.
// Bu aslında "AA'yı implemente eden isimsiz bir class" oluşturur.
interface a{
    void m1();
}
interface b extends a{
    void m2();
}
interface c extends a{
    default void m1(){
        System.out.println("Default m1 method in interface c");
    }
}
class M{
    public static void main(String[] args) {
        a obj = new a() {
            @Override
            public void m1() {
                System.out.println("m1 method implementation");
            }
            public void m2() {
                System.out.println("m2 method implementation");
            }
            /*public void m3() {
                System.out.println("m3 method implementation");
            }*/
        };
        obj.m1();
        obj.m2(); // This will now work since m2 is implemented in the anonymous class
        //obj.m3(); // This will cause a compile-time error since m3 is not defined in interface a

        //------------------------------------------------------------------------------------------------

        a obj2 = new c(){
            // No need to override m1() since c provides a default implementation
        };
        obj2.m1(); // This will call the default implementation from interface c

        //------------------------------------------------------------------------------------------------
        
        a obj3 = new c(){
            @Override
            public void m1() {
                System.out.println("Overridden m1 method in anonymous class");
            }
        };
        obj3.m1(); // This will call the overridden m1 method in the anonymous class
    }
}
```

### Örnek 4
```java
interface A {
    void kos(); // İçi boş (Abstract)
}

interface B extends A {
    // A'dan gelen kos() metodunu override edip içini dolduruyoruz.
    // DİKKAT: Bunu yapabilmek için 'default' yazmak ZORUNDASIN.
    @Override
    default void kos() {
        System.out.println("B arayüzü dedi ki: Hafif tempoda koşuluyor.");
    }
}

// C sınıfı artık kos() metodunu yazmak ZORUNDA DEĞİL.
// Çünkü B interface'i o işi halletti.
class C implements B {
    // İçi boş bırakılabilir, B'deki kod çalışır, override edilmek zorunda değil.
}

public class Main {
    public static void main(String[] args) {
        C c = new C();
        c.kos(); // Çıktı: B arayüzü dedi ki: Hafif tempoda koşuluyor.
    }
}
```

### Örnek 5
```java
interface A {
    default void konus() { System.out.println("A konuşuyor"); }
    default void konus2() { System.out.println("A konuşuyor 2"); }
}

interface B {
    default void konus() { System.out.println("B konuşuyor"); }
    default void konus2() { System.out.println("B konuşuyor 2"); }
}

// C sınıfı ikisini de implemente ediyor
class C implements A, B {
    
    @Override
    public void konus() {
        // HATA! Sadece super.konus() diyemezsin. 
        // Java sorar: "Hangi super? A mı B mi?"
        
        // ÇÖZÜM: Interface adını belirterek super kullanmak
        A.super.konus(); // "A konuşuyor"
        B.super.konus(); // "B konuşuyor"

        
        
        System.out.println("Son sözü C söyler.");
    }
    public static void main(String[] args) {
        C c = new C();

        c.konus(); // "A konuşuyor", "B konuşuyor", "Son sözü C söyler."

        //c.konus2(); // HATA! Hangi konus2? A mı B mi? hiçbir interface'e override etmediğimiz için belirsizlik var.
        c.A.super.konus2(); // "A konuşuyor 2"
        c.B.super.konus2(); // "B konuşuyor 2"
    }
}
```

### Örnek 6
```java
interface I{
    int a = 1;

    void m();
}
interface I2{
    int a = 2;
    int b = 3;

    void m();

    default void show(){
        System.out.println(1);
    }
    static void show2(){
        System.out.println(2);
    }
}
class A implements I, I2{
    public void m(){
        System.out.println(3);
    }
    public void show(){
        System.out.println(4);
    }
    public static void main(String[] args){
        A a = new A();
        a.m();  // print 3 çünkü I1'de türetilmiş, I2 devam ettirmiş, bu da I2'den override etmiş
        a.show();   // print 4 çünkü I2'den override edilmiş
        I2 i2 = new A();    // Polymorphism örnek. Çabuk hatırlatma: Statik fonksiyonlar I2'den, override edilenler A'dan, değişkenler(yok ama olsaydı normal bir class yapısında) override olmadığından I2'den
        i2.m(); // print 3 çünkü A'dan türetilmiş
        i2.show();  // print 4 çünkü A'dan türetilmiş ve override edilmiş.
        i2.show2(); // ÇOK ÖNEMLİ: runtime error: çünkü show2 statik olduğu için yalnız sınıfı üzerinden çağırılabilir. INTERFACE STATIC METOTLAR ASLA INSTANCE ÜZERİNDEN ÇAĞIRILMAZ.
        I2.show2(); // statik metot olduğu için I2'den çalışmakta.
        System.out.println(I1.a);
        System.out.println(I2.a);
        System.out.println(a);  // runtime error: hangi interface'den çekeceğini bilemez.
        System.out.println(b);  // direkt olarak I2'den çekebilir.
    }
}
```

### Örnek 7
```java
interface A{
    int i=111;
}
class B implements A{
    void f(){
        i=222;  // YASAK
    }
    public static void main(String[] args){
        Y y= new Y();
        y.f();  // Olmaz çünkü A içinde tanımlanan i değeri final olarak tanımlandığ için f() içerisinde değiştirilmesine izin verilmez.
    }
}
```

### Örnek 8
```java
interface Veritabani {
    static void baglantiKur() {
        System.out.println("Interface: Veritabanına bağlanıldı.");
    }
}

class PostgreSQL implements Veritabani {
    // Kendi metodumuz...
}

public class Main {
    public static void main(String[] args) {
        PostgreSQL db = new PostgreSQL();
        
        // ❌ HATA 1: Instance üzerinden çağıramazsın! (Compile Error)
        db.baglantiKur(); 
        
        // ❌ HATA 2: Alt sınıf ismiyle de çağıramazsın! (Compile Error)
        PostgreSQL.baglantiKur(); 

        // ✅ TEK DOĞRU YOL (Zorunluluk): Sadece Interface adıyla çağrılır!
        Veritabani.baglantiKur(); 
    }
}
```

## UML

Unified Modeling Language anlamına gelir. Class şeması hakkında bilgi sağlar. Aşağıda örnek:

_____________________
Human OR 

<< abstract >> Human OR 

<< interface >> Human
_____________________
-age: int
+name: String
#salay: double
_____________________
+getName(): String
-privFunction(): int


Extends ilişkileri içi boş oklar ile gösterilir. Implements ilişkileri ise kesikli içi boş oklar ile gösterilir.

## Inner Class

Inner class'lar iki türden olabilir, member ve static şeklinde. LinkedList yazarken inner class olarak Node kullanılması mesela örnektir, outer class'ın daha self-contained olmasını sağlar. Inner ve outer class'lar birbirlerinin private metot ve instance'larına erişebilirler.

Tekli inner instance'ı oluşturulamaz, sonuçta bir class olmasına rağmen sonuçta yine de onu tutan outer class'ın bir parçası, dolayısı ile Outer.Inner obj = (önceden oluştutulmuş olan outer objesi).new Inner();

Member inner classlar'da iki sıkıntı var. Inner class'taki static değişkenler yalnızca final olarak tanımlanabilir ve inner class'ların static metotlarda oluşturulamaması. O yüzden static inner class devreye girer, bu şeklide static olduğu için de tabii ki outer instance'ı olmadan dışarıdan erişilebilir hale gelir, aynı şekilde static variable ve metotlar ile de. Dolayısı ile akıllara ilk gelen soru olan private static class olabilir mi sorusuna cevap hayırdır yoksa dışarıdan da çağırılamaz olur outer instance'ı kullanılmadan.

Bir class outer'ı extends ederse aynı şekilde o outer'ın inner'ına da sahip olmaktadır.

Bir objeye ihtiyacımız olduğunda ama class'ına gerek olmadığında anonymous class'lar kullanılabilir.

### Örnek 1
```java
interface trickyInterface{ void print();}

class Outer{
    private int x=42;
    private int y=100;

    private class Inner implements trickyInterface{
        private int x=6;
        private int y=0;
        public void print(){
            System.out.println("Inner print, inner x: " + x);    // 6
            System.out.println("Inner print, inner y: " + y);    // 0
            System.out.println("Inner print, this.x: " + this.x); // 6, buradaki this.x, Inner class'ındaki x'i ifade eder, Outer class'ındaki x'i değil.
            System.out.println("Inner print, this.y: " + this.y); // 0, buradaki this.y, Inner class'ındaki y'yi ifade eder, Outer class'ındaki y'yi değil.
            // System.out.println("Inner print, outer.x: " + Outer.x); // SYNTAX ERROR, Cannot make a static reference to the non-static field Outer.x
            // System.out.println("Inner print, outer.y: " + Outer.y); // SYNTAX ERROR, Cannot make a static reference to the non-static field Outer.x
            System.out.println("Inner print, outer.this.x: " + Outer.this.x); // 42, new Outer().x de olur
            System.out.println("Inner print, outer.this.y: " + Outer.this.y); // 100, new Outer().y de olur
        }
    }
    public void print(){
        Inner inner = new Inner();
        inner.print();
        System.out.println("Outer print, this.x: " + this.x);   // 42, x ile de çağırılabilir.
        System.out.println("Outer print, this.y: " + this.y);   // 100, y ile de çağırılabilir.
        System.out.println("Outer print, inner.x: " + inner.x); // 6
        System.out.println("Outer print, inner.y: " + inner.y); // 0
        // System.out.println("Outer print, inner.this.x: " + inner.this.x); // SYNTAX ERROR
        // System.out.println("Outer print, inner.this.y: " + inner.this.y); // SYNTAX ERROR
    }
    public trickyInterface getInnerClass(){
        return (trickyInterface) new Inner();
    }
}
public class M{
    public static void main(String[] args){
        Outer outer = new Outer();
        outer.print();

        // Hayvani tricky bi yöntem private class'ı çekebilmek için.
        trickyInterface tricky = outer.getInnerClass();
        tricky.print();
        
        //Inner inner = new Inner(); Bu şekilde çağırılamaz
        //Outer.Inner inner = new Inner(); // SYNTAX ERROR, Cannot make a static reference to the non-static inner class Outer.Inner
        //Outer.Inner inner = outer.new Inner(); // Normalde kullanılabilir ancak Inner class'ı private olduğu için bu satırda da SYNTAX ERROR alırız, çünkü Outer class'ının dışından Inner class'ına erişmeye çalışıyoruz. Public olsaydı bu şekilde erişebilirdik.
        //Outer.Inner inner = new Outer().new Inner(); // Normalde kullanılabilir ancak Inner class'ı private olduğu için bu satırda da SYNTAX ERROR alırız, çünkü Outer class'ının dışından Inner class'ına erişmeye çalışıyoruz. Public olsaydı bu şekilde erişebilirdik.
    }
}
```

### Örnek 2
```java
class Outer{
    int data = 10;
    class Inner{
        void msg(){
            System.out.println("data is "+data);
        }
    }
}
public class Main{
    public static void main(String[] args) {
        Outer obj = new Outer();
        Outer.Inner in = obj.new Inner();
        in.msg();   // 10
    }
}
```

### Örnek 3
```java
class B{
    private C[] arr = new C[2];

    public class C{
        private int y;
        public void print(){
            System.out.println("y: " + y);
        }
        C(){
            this.y = 0;
        }
        C(int y){
            this.y = y;
        }
    }

    B(){
        arr[0] = new C(1);
        arr[1] = new C(2);
    }

    void print(){
        for(C c : arr){
            c.print();
        }
    }
}
public class Main {
    public static void main(String[] args) {
        B b = new B();
        b.print();
        //B c = b.new C();  // Şeklinde bir tanımlama yapılamaz. Burada polymorphism kuralları uygulanamaz. İlla ki b.new C() yapılacaksa tanımlanacak olan tür de B.C olmalı.
        B.C c = b.new C();  // Parametresiz C() oluşturuluyor, y değeri 0 olarak atanır ve print() metodu çağrıldığında override edilmiş olan print() metodu çalışır ve y: 0 yazdırılır.
        c.print();
    }
}
```

### Örnek 4
```java
class Outer{
    private int x = 42;
    public static class Inner{
        private int x = 24;
        public static int s = 0;

        public static void print2(){
            System.out.println("Inner static print2, s: " + s + ", " + Inner.s) ; //this.s olmaz, this non-static'ler içindir.
            System.out.println("Inner static print2, inner x: " + new Inner().x);
            System.out.println("Inner static print2, outer x: " + new Outer().x);            
        }
        public void print(){
            System.out.println("Inner non-static print, s: " + s + " ," + this.s + ", " + Inner.s); // this.s veya Inner.s de yazılabilir ama static olduğu için this kullanmaya gerek yok, s yazmak yeterli
            System.out.println("Inner non-static print, x: " + x + ", " + this.x + ", " + new Inner().x);  //this.x de olabilir
            //System.out.println("Inner non-static print, x: " + Outer.this.x); // error: inner class static olduğu için outer class'ın instance variable'larına erişemez, bu yüzden this.x ile erişmeye çalıştık ama o da inner class'ın x'ine eriştiği için hata verdi, eğer outer class'ın x'ine erişmek istiyorsak Outer.this.x şeklinde erişmemiz gerekiyor, ancak inner class static olduğu için bu da mümkün değil
            System.out.println("Inner non-static print, x: " + new Outer().x);  //  Outer.this.x olmaz çünkü statik class'tan non-static class'a çıkamayız this ile.
        }
    }

    public void print(){
        Inner inner = new Inner();
        inner.print();
        System.out.println("Outer non-static print, x: " + x);
    }
}
public class M {
    public static void main(String[] args) {
        Outer outer = new Outer();
        outer.print();

        Outer.Inner inner = new Outer.Inner();
        inner.print();

        Outer.Inner.print2();
        Outer.Inner.s = 42;

        /**
         * ÇOK ÖNEMLİ NOT: STATIC CLASS OUTER CLASS TARAFINDAN OLUŞTURULURKEN:
         *      NEW OUTER.INNER();      ŞEKLİNDE OLUŞTURULUR, BİR INSTANCE ÜZERİNDEN OLUŞTURULAMAZ VEYA NEW OUTER().INNER() ŞEKLİNDE OLUŞTURULAMAZ. BU KURAL DİREKT EZBERLEMELİK GALİBA?
         * */
    }
}
```

### Örnek 5
```java
class A{
    private String name = "A";

    public void print(){
        System.out.println(name);
    }
    
    class B{
        private String name = "B";

        public void print(){
            System.out.println(name);
            System.out.println(A.this.name);
        }

        class C{
            private String name = "C";

            public void print(){
                System.out.println(name);
                System.out.println(B.this.name);
                System.out.println(A.this.name);
            }
        }

        static class D{
            private String name = "D";

            public void print(){
                System.out.println(name);
                // System.out.println(B.this.name); // This line is invalid for static nested class, statik olmayan B sınıfına erişemez.
                // System.out.println(A.this.name); // This line is invalid for static nested class, statik olmayan A sınıfına erişemez.
                
            }
        }
    }
}
public class Main{
    public static void main(String[] args) {
        A a = new A();
        A.B b = a.new B();
        A.B.C c = b.new C();
        A.B.D d = new A.B.D();
        c.print();
        b.print();
        a.print();
        
        d.print();

    }
}
```

### Örnek 6
```java
class C{
    public int x = 42;
    void m(){
        System.out.println("Hello");
    }
}
class B{
    public int x = 24;
    class Inner extends C{
        void m(){
            System.out.println("World");
        }
    }
}
public class M {
    public static void main(String[] args) {
        B b = new B();
        B.Inner inner = b.new Inner();
        System.out.println(inner.x);    // çıktı olarak 42 verir çünkü Inner sınıfı C sınıfını genişletir ve x değişkenini miras alır.
        inner.m();  //  prints "World"
    }
}
```

### Örnek 7
```java
class A{
    int a = 2;
    void print(){
        System.out.println("A");
    }
}
abstract class B{
    abstract void print();
}
interface I{
    void m();
}


public class M{
    public static void main(String[] args) {
        new A(){
            public void print2(){
                System.out.println("A new print");
            }
        }.print2();
        A a = new A() {
            void print2(){
                System.out.println("A new");
            }
        };
        A a3 = new A(){
            void print2(){
                System.out.println("A newest");
            }
        }.print2(); // A a gibi aynı şekilde bu da çalışmaz çünkü a3'ün tipi A ve A sınıfında print2() metodu yok, o yazılan print2'nın kullanılabilmesi için A sınıfında önceden print2 tanımlanmalıydı, aşağıdaki örnekte print() metodunun override edilmiş hali var ve o çalışır.
        // a.print2(); // çalışmaz çünkü a'nın tipi A ve A sınıfında print2() metodu yok, o yazılan print2'nın kullanılabilmesi için A sınıfında önceden print2 tanımlanmalıydı, aşağıdaki örnekte print() metodunun override edilmiş hali var ve o çalışır.

        A a2 = new A(){
            int a = 3;
            void print(){
                System.out.println("A anonymous print");
            }
        };
        a2.print(); // çalışır çünkü a2'nin tipi A ve A sınıfında print() metodu var, o yazılan print() metodu A sınıfındaki print() metodunu override eder ve a2.print() çağrıldığında o override edilen print() metodu çalışır.
        System.out.println(a2.a);   // Anonymous class'ta a tekrar tanımlanmış olsa bile burada da değişkenler üzerinden override işlemi olmaz.


        B b = new B(){
            void print(){
                System.out.println("B");
            }
            void print2(){
                System.out.println("B print2");
            }
        };
        b.print();
        //b.print2(); // çalışmaz çünkü b'nin tipi B ve B sınıfında print2() metodu yok, o yazılan print2'nın kullanılabilmesi için B sınıfında önceden print2 tanımlanmalıydı veya print() içerisinde print2() çağırılmalıydı, aşağıdaki örnekte print() metodunun override edilmiş hali var ve o çalışır. Aynı yukarıdaki A a; örneğinde olduğu gibi.
        I i = new I(){
            public void m(){
                System.out.println("I");
            }
        };
        i.m();
    }
}
```

### Örnek 8
```java
class A{
    int a = 2;
    class B{
        int a = 3;
        class C extends A{
            void m(){
                System.out.println(a);
            }
        }
    }
}
public class Main{
    public static void main(String[] args) {
        new A().new B().new C().m();    // Burada C, B'nin alt sınıfı olmasına rağmen A'yı extend ettiği için onun a'sını kullanır, çıktı 2 olur. Java'da öncelikle inherit basamaklarına bakılır ondan sonra outer class'lara bakılır.
    }
}
```
### Örnek 9
```java
public class Outer {
    private static int count = 5;
    static class StaticInner {
        void showCount() {
            System.out.println("Count: " + count);
        }
    }
    public static void main(String[] args) {
        Outer outer = new Outer();
        //Outer.StaticInner inner = outer.new StaticInner();    // Bu şekilde çağrılamaz çünkü StaticInner static bir sınıftır ve outer instance'ına bağlı değildir.
        Outer.StaticInner inner = new Outer.StaticInner(); // Bu şekilde olabilir outer instance'ı oluşturmadan.
        inner.showCount();
        System.out.println(Outer.count);
    }
}
// Compile Error: StaticInner bir static sınıftır. Static nesneler, sınıfın bir instance'ına değil, sınıfın kendisine aittir. Bu yüzden outer.new StaticInner() şeklinde bir instance üzerinden çağrılamazlar. 
// "Outer.StaticInner inner = new Outer.StaticInner();" şeklinde çağırılmalıdır.
```

## Exception Handling

Adı üstünde. Inputlar'da, I/O'larda, network bağlantılarında kullanılır.

- IOException
- FileNotFoundException
- NullPointerException
- ArithmeticException

### Örnek 1

```java
public static void check(String str) throws Exception{
    if(str == null)
        throw new Exception("String is null")
}
```

### Örnek 2
```java
public class AA{
    public static void main(String[] args) {
        Example example = new Example();
        try {
            example.doSomething(-5);    // Eğer aritmetik bir hata veya başka bir beklenmedik durum oluşursa negative'e girmez. Exception catch bloğuna düşer. Exception bloğu olmasaydı onu yakalayacak bir catch bloğu olmadığı için program çökerdi, çökmeden de finally bloğunu çalıştırırdı. 
            System.out.println("This line will not be executed if an exception is thrown above.");
        } catch (NegativeException e) {
            System.out.println("Caught NegativeException: " + e.getMessage());
        } catch(Exception e) {
            // catch içerisinde aşağıdaki tüm fonksiyonları kullanarak hatayı detaylı bir şekilde inceleyelim
            System.out.println(e); // e.toString() fonksiyonunu çağırır.
            System.out.println("An unexpected error occurred: " + e.getMessage());
            System.out.println("Class of the exception: " + e.getClass().getName());
            System.out.println("Instance of Exception: " + (e instanceof Exception));
            System.out.println("Stack trace:");
            e.printStackTrace();

            StackTraceElement[] stackTrace = e.getStackTrace();
            for (StackTraceElement element : stackTrace) {
                System.out.println(element.getFileName() + ":" + element.getLineNumber() + " - " + element.getMethodName());
            }
        } finally {
            System.out.println("This block will always execute, regardless of exceptions.");
        }
    }
}
class NegativeException extends Exception{
    public NegativeException(String message) {
        super(message);
    }
}
class NoFuelException extends Exception{
    private String fuelType;

    public NoFuelException(String message, String fuelType) {
        super(message);
        this.fuelType = fuelType;
    }
    public String getFuelType() {
        return fuelType;
    }
}
class Example{
    public void doSomething(int number) throws NegativeException {
        if (number < 0) {
            throw new NegativeException("Negative number is not allowed: " + number);
        }
        System.out.println("Number is: " + number);
    }
}
```

### Örnek 3

```java
import java.util.Scanner;
import java.util.InputMismatchException;

public class AA {
    public static int getIntegerInput(Scanner scanner){
        int userInput = 0;
        boolean validInput = false;

        // Düzgün bir tamsayı girişi alınana kadar döngü devam eder
        while(!validInput){
            try{
                System.out.print("Please enter an integer: ");
                userInput = scanner.nextInt();
                validInput = true; // Exit loop if input is valid
            } catch(InputMismatchException e){
                System.out.println("Invalid input. Please enter an integer.");
                scanner.next(); // Clear the invalid input
            }
        }
        return userInput;
    }
    
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int userInput = getIntegerInput(scanner);
        System.out.println("You entered: " + userInput);
        scanner.close();
    }
}
```

### Örnek 4
Throwable hiyerarşisi:

Object

|_ Throwable

|___ Exceptions

|_____ BlaBlaExceptions

|___ Error

|_____ Virtual Machine Error bla bla
```java
public class M{
    public static void throwThrowable() throws Throwable {
        // Parent class olduğu için çağırılabilir.
        throw new Throwable();
    }
    public static void throwException() throws Exception {
        // Parent class olduğu için çağırılabilir.
        throw new Exception();
    }
    public static void throwRuntimeException() throws RuntimeException {
        // Parent class olduğu için çağırılabilir.
        throw new RuntimeException();
    }
    public static void throwError() throws Error {
        // Parent class olduğu için çağırılabilir.
        throw new Error();
    }
    public static void main(String[] args) {
        try {
            throwThrowable();
        } catch (Throwable e) {
            System.out.println("Caught Throwable: " + e);
        }

        try {
            throwException();
        } catch (Exception e) {
            System.out.println("Caught Exception: " + e);
        }

        try {
            throwRuntimeException();
        } catch (RuntimeException e) {
            System.out.println("Caught RuntimeException: " + e);
        }

        try {
            throwError();
        } catch (Error e) {
            System.out.println("Caught Error: " + e);
        }
    }
}
```

### Örnek 5

```java
public class M{
    public static void throwExceptionA(){
        throw new Exception("Exception"); // SYNTAX ERROR, ya catch ya da declare edilmelidir. Aşağıdaki iki örnekli şekilde olabilir.
    }

    public static void throwExceptionB(){
        try{
            throw new Exception("Exception");
        } catch (Exception e) {
            System.out.println("Catched exception"); // Output olarak bu satır elde edilir. Throw edilen exception'nun içindeki string kullanılmaz. Onu kullanmak için aşağıdaki kod satırı kullanılabilir.
            System.out.println(e.getMessage()); // "Exception"  
        }
    }   

    public static void throwExceptionC() throws Exception{
        throw new Exception("Exception"); // Herhangi bir output vermez. Direkt exception ile kodu crash eder. Aşağıdaki şekilde bir error atar.
        /**
         * Exception in thread "main" java.lang.Exception: Exception
            at Main.throwException(Main.java:3)
            at Main.main(Main.java:8)
        */
    }

    //  Checked Exception kuralları
    public static void throwEverythingConditional(int i){
    /**
     * Unchecked demek ki throws yazmaya gerek yok demektir. Checked exception'larda throws yazmak zorunludur.
     * 
     * Checked Exceptions: Throwable, Exception, blablaException....
     * Unchecked Exceptions: Error, RuntimeException(s)
     */
        if(i==0)
            throw new Throwable();  // SYNTAX ERROR: Throwable bu işin babası olduğundan checked'dır dolayısı ile throws Throwable yazılmak zorunda.
        if(i==1)
            throw new Error();  // Unchecked durumda olduğu için hata atmaz. Java burada throws beklemez.
        if(i==2)
            throw new Exception();  // SYNTAX ERROR: Throwable ile benzer problem, işin babası olduğundan checked'dır dolayısı ile throws Exception(veya throws Throwable de yeterli üst class'ı olduğu için) yazılmak zorunda.
        if(i==3)
            throw new RuntimeException();   // Error ile aynı mantık, unchecked olduğu için temiz bir şekilde çalışmaktadır.

        // Tabii dümdüz throws Throwable eklenirse Exception hatasını da çözer çünkü daha üst bir class.
    }
}
```

### Örnek 6
```java
/**
 * Child class parent'ının koyduğu kuralı gevşetemez, ya daraltabilir ya kaldırabilir ya da aynısını koyabilir ama genişletemez. 
 * Interface'lerde de aynı durum geçerlidir. Bir interface'i implement eden class, interface'in koyduğu kuralı gevşetemez, ya daraltabilir ya kaldırabilir ya da aynısını koyabilir ama genişletemez.
*/


import java.io.FileNotFoundException;
import java.io.IOException;

class A {
    void f() throws IOException{}
}

class B extends A{
    void f() throws FileNotFoundException{}
}

class C extends A{
    void f() throws Exception{} // SYNTAX ERROR
}

class D extends A{
    void f() {}
}


interface F{
    void f() throws IOException;
}
class G implements F{
    public void f() throws FileNotFoundException{}
}
class H implements F{
    public void f() throws Exception{} // SYNTAX ERROR
}
```

### Örnek 7
```java
public class AA{
    String str = "a";
    public static void main(String[] args) {
        new AA.A();
    }

    void A(){
        try{
            str += "b";
            B();
        } catch (Exception e){
            str += "c";
        }
        System.out.println(str);
    }
    void B() throws Exception{
        try{
            str += "d";
            C();
        } catch (Exception e){
            return;
        } finally{
            str += "e";
        }
        str += "f";
    }
    void C() throws Exception{
        throw new Exception();
    }
}

//OUTOUTPUT: abde
```

### Örnek 7
```java
class ExceptionTest{
    public static void main(String[] args){
        try{
            method1();
            System.out.print("A");
        } catch(CustomException ex){
            System.out.print("B");
            return;
        } finally{
            System.out.print("C");
        }
        System.out.print("D");
    }
    public static void method1() throws CustomException{
        try{
            throw new CustomException();
        } catch(CustomException ex){
            System.out.print("E");
            throw ex;
        } finally{
            System.out.print("F");
        }
    }
}
class CustomException extends Exception{
    CustomException() {
        super("Z");
    }
}

//OUTPUT: EFBC
```

### Örnek 8
```java
class AA{
    public static void main(String[] args) {
        System.out.println(AA.test());
    }
    public static String test(){
        try{
            System.out.println("try");
            throw new Exception();
        } catch (Exception e){
            System.out.println("catch");
            return "return in catch";
        } finally {
            System.out.println("finally");
            return "return in finally";
        }
    }
}

// OUTPUT: try, catch, finally, return in finally 
```
### Örnek 9
```java
static void m2(){
    try{
        System.out.println("TRYING m1");
        m1();
    }
    catch(Exception e){
        System.out.println("CAUGHT1");
    }
    catch(IOException e){
        System.out.println("CAUGHT2");
    }
    finally{
        System.out.println("FINALLY");
    }
}
// syntax error atar çünkü catch exception sıraları hiyerarşiye uygun olmalıdır.
```
### Örnek 10
```java
public class Outer {
    public static void main(String[] args) {
        m();
    }
    static void m() throws Throwable{
        throw new Exception();
    }
}

// Bu şekilde m() içerisinde sıkıntı yok gibi gözükmese de onu main çağırıyor ve main'de bu handle edilmiyor. main() içerisinde bunun try-catch ile handle edilmesi veya main() throws Throwable eklenmesi gerekir. 
```

## Karışık Örnekler

### Örnek 1
```java
class Baba {
    static int x = 10;
    int y = 20;
    
    static { System.out.print("B1 "); }
    
    Baba() { System.out.print("B2 "); }
    
    static void statikMetot() { System.out.print("B3 "); }
    
    void normalMetot() { System.out.print("B4 "); }
}

class Cocuk extends Baba {
    // DİKKAT: final static
    public final static int z = 5; 
    
    static int x = 100;
    int y = 200;
    
    static { System.out.print("C1 "); }
    
    Cocuk() { System.out.print("C2 "); }
    
    static void statikMetot() { System.out.print("C3 "); }
    
    @Override
    void normalMetot() { System.out.print("C4 "); }
}

public class SinavSoru1 {
    public static void main(String[] args) {
        // Adım 1
        System.out.print(Cocuk.z + " ");
        // Statik bloklar çalışmaz, çıktı 5
        
        // Adım 2
        Baba obj = new Cocuk();
        // B1 C1 B2 C2
        
        // Adım 3
        System.out.print(obj.x + " ");
        // 10
        System.out.print(obj.y + " ");
        // 20
        
        // Adım 4
        obj.statikMetot();
        // B3
        obj.normalMetot();
        // C4
    }
}
```

### Örnek 2

```java
public class SinavSoru2 {
    static String s = "";

    public static void main(String[] args) {
        System.out.println(test());
        // 2
        System.out.println(s);
        // ABCDE
    }

    static int test() {
        try {
            s += "A";
            throw new RuntimeException();
        } catch (Exception e) {
            s += "B";
            try {
                throw new ArithmeticException();
            } catch (ArithmeticException ae) {
                s += "C";
                return 1; 
            } finally {
                s += "D";
                throw new NullPointerException(); 
            }
        } finally {
            s += "E";
            return 2; 
        }
    }
}
```

### Örnek 3

```java
interface Kurallar {
    static void kuralKoy() { System.out.print("K1 "); }
    default void kos() { System.out.print("K2 "); }
}

class Dis implements Kurallar {
    int sayi = 10;
    
    class Ic {
        int sayi = 20;
        void goster() {
            System.out.print(sayi + " ");
            System.out.print(Dis.this.sayi + " ");
        }
    }
    
    @Override
    public void kos() {
        System.out.print("D1 ");
    }
}

public class SinavSoru3 {
    public static void main(String[] args) {
        Kurallar nesne = new Dis();
        
        // Satır 1
        nesne.kos();
        // D1
        
        // Satır 2
        nesne.kuralKoy();
        // ERROR ATAR, KuralKoy metodu interface static olduğu için yalnızca interface adı üzerinden çağırılabilir, instance üzerinden çağırılamaz
        
        // Satır 3
        Dis.Ic ic = new Dis().new Ic();
        ic.goster();
        // 20 10
    }
}
```

### Örnek 4
```java
class Silah {
    String mermi = "9mm (Silah)";
    
    void atesEt() {
        System.out.print("Silah Ateşledi: " + this.mermi + " | ");
    }
}

class Tufek extends Silah {
    // Variable Hiding! (Değişken Gizleme)
    String mermi = "5.56mm (Tufek)"; 
    
    @Override
    void atesEt() {
        System.out.print("Tüfek Ateşledi: " + this.mermi + " | ");
        System.out.print("Tüfek Super: " + super.mermi + " | ");
    }
}

public class SinavSoru4 {
    public static void main(String[] args) {
        Silah s = new Tufek();
        
        // Adım 1
        System.out.print(s.mermi + " | ");
        // 9mm
        
        // Adım 2
        s.atesEt();
        // tüfek ateşledi 5.56mm | tüfek süper 9mm |
    }
}
```

### Örnek 5
```java
class Batarya {
    int kapasite;
    Batarya(int k) { this.kapasite = k; }
}

class Telefon implements Cloneable {
    String model;
    Batarya batarya;

    Telefon(String model, int kapasite) {
        this.model = model;
        this.batarya = new Batarya(kapasite);
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone(); // Shallow Copy (Yüzeysel Klonlama)
    }
}

public class SinavSoru5 {
    public static void main(String[] args) throws Exception {
        Telefon t1 = new Telefon("Nokia", 1000);
        
        // Klonlama yapılıyor
        Telefon t2 = (Telefon) t1.clone();
        
        // Klonlanan nesne üzerinde değişiklikler:
        t2.model = "Samsung";
        t2.batarya.kapasite = 5000;
        
        // Referans ataması ve kritik değişiklik!
        Telefon t3 = t1;
        t3.batarya = new Batarya(2000);
        // 🚨 DİKKAT!
        
        // Çıktılar:
        System.out.print(t1.model + " ");
        // Nokia
        System.out.print(t1.batarya.kapasite + " ");
        // 2000 
        System.out.print(t2.batarya.kapasite + " ");
        // 5000
    }
}
```

### Örnek 6
```java
abstract class Hayvan {
    abstract void sesCikar();
}

public class SinavSoru6 {
    public static void main(String[] args) {
        
        Hayvan h = new Hayvan() {
            @Override
            void sesCikar() {
                System.out.print("Hav ");
                kuyrukSalla(); 
            }
            
            void kuyrukSalla() {
                System.out.print("Salla ");
            }
        };
        
        h.sesCikar();
        // Hav Salla print'letir. 
        
        // h.kuyrukSalla(); bu çağırılamaz çünkü error atar çünkü Hayvan sınıfında yok bu aslında bizim anonymous'ta var böyle çağırılamaz. ancak anaonymous'taki sesCikar() kuyrukSalla()'yı çağırabilir.
    }
}
```

### Örnek 7
```java
class A {
    int deger = 10;
    
    class B {
        int deger = 20;
        
        class C extends A {
            void goster() {
                System.out.print(deger + " | ");
                System.out.print(super.deger + " | ");
                System.out.print(B.this.deger + " | ");
                System.out.print(A.this.deger + " | ");
            }
        }
    }
}

public class SinavSoru1 {
    public static void main(String[] args) {
        A a_nesnesi = new A();
        A.B b_nesnesi = a_nesnesi.new B();
        A.B.C c_nesnesi = b_nesnesi.new C();
        
        c_nesnesi.goster();
        // 10 10 20 10 
    }
}
```

### Örnek 8
```java
class X {}
class Y extends X {}
class Z extends Y {}

class Islemci {
    static void islemYap(X obj) { System.out.print("X-Param "); }
    static void islemYap(Y obj) { System.out.print("Y-Param "); }
}

public class SinavSoru2 {
    public static void main(String[] args) {
        X ref1 = new Y();
        Y ref2 = new Z();
        
        Islemci.islemYap(ref1);
        // X-Param
        Islemci.islemYap(ref2);
        // Y-Param
        
        try {
            Z ref3 = (Z) ref1;
            Islemci.islemYap(ref3);
            // error atar, dededen torun inheritance sağlanmaz hata atar.
        } catch (ClassCastException e) {
            System.out.print("Hata1 ");
        }
        
        try {
            Y ref4 = (Y) ref1;
            Islemci.islemYap(ref4);
            // Y-Param
        } catch (ClassCastException e) {
            System.out.print("Hata2 ");
        }
    }
}
```

### Örnek 9
```java
interface I1 {
    int SAYI = 100;
    default void yaz() { System.out.print("I1-Yaz "); }
}

interface I2 {
    int SAYI = 200;
    default void yaz() { System.out.print("I2-Yaz "); }
    static void dur() { System.out.print("I2-Dur "); }
}

class Temel implements I1, I2 {
    @Override
    public void yaz() {
        I2.super.yaz();
        System.out.print("Temel-Yaz ");
    }
    
    void testEt() {
        // L1: System.out.print(SAYI + " ");
        System.out.print(I1.SAYI + " ");
    }
}

public class SinavSoru3 {
    public static void main(String[] args) {
        Temel t = new Temel();
        
        t.yaz();
        // I2-Yaz Temel-Yaz
        t.testEt();
        // 100
        
        t.dur();
        // error atar, interface instance üzerinden değişken çekilmez
        I2.dur();
        // I2-Dur
    }
}
```

### Örnek 10
```java
public class SinavSoru1 {
    static int x = 0;

    static void f(int n) {
        if (n <= 1) {
            System.out.print("D" + x + " ");
            return;
        }
        x++;
        System.out.print("G" + n + " ");
        
        f(n - 2);
        
        x++;
        System.out.print("C" + n + "-" + x + " ");
    }

    public static void main(String[] args) {
        f(5);
        // G5 G3 D2 C3-3 C5-4
    }
}
```

### Örnek 11
```java
class Kutu {
    int boyut = 10;
    //public final static int boyut = 10;   // şeklinde olsaydı 0 değil bu basılırdı çünkü final static direkt memo'ya yazılır erişim hemen sağlanır. normal değişkenlerin tanımlanması için const metodun bitmesi beklenir.
    
    Kutu() {
        ac();
    }
    
    void ac() {
        System.out.print("Kutu:" + boyut + " ");
    }
}

class SihirliKutu extends Kutu {
    int boyut = 50;
    
    SihirliKutu(int boyut) {
        this.boyut = boyut;
        ac();
    }
    
    @Override
    void ac() {
        System.out.print("Sihirli:" + boyut + " ");
    }
}

public class SinavSoru2 {
    public static void main(String[] args) {
        Kutu k = new SihirliKutu(100);
        System.out.print("Son:" + k.boyut + " ");
        // Sihirli: 0, Sihirli: 100, Son: 10
    }
}
```

### Örnek 12
```java
import java.io.IOException;

class AnaSistem {
    AnaSistem(String ad) {
        System.out.print("AnaSistem(" + ad + ") ");
    }
    
    void baslat() throws IOException {
        System.out.print("Sistem Çalışıyor ");
    }
}

class AltSistem extends AnaSistem {
    
    AltSistem() {
        System.out.print("AltSistem Hazırlanıyor ");
    }
    
    AltSistem(String ad) {
        this();
        System.out.print("AltSistem(" + ad + ") ");
    }
    
    @Override
    void baslat() throws Exception { 
        System.out.print("AltSistem Çalışıyor ");
    }
}

public class SinavSoru3 {
    public static void main(String[] args) {
        try {
            AnaSistem a = new AltSistem("Motor");
            a.baslat();
        } catch (Exception e) {
            System.out.print("Hata Yakalandı ");
        }
    }
}
/**
 * Öncelikle 2 hata var:
 *      AnaSistem() boş const olmadığı için AltSistem const'larında super() otomatik olarak çağırılınca Java nereye gideeceğini bilemez hata atar. Öncelikle bu handle edilmelidir.
 *      İkinci olarak da AltSistemin attığı Exception türü base sınıftan gelen exception türünden daha geniş, buna da izin verilmez.
 * 
 * Eğer bu iki hata düzeltilirse çıktı: AltSistem Hazırlanıyor, AltSistem(Motor), AltSistem Çalışıyor.
*/
```

### Örnek 13
```java
class Ata {
    int sayi = 1;
    
    Ata() {
        goster();
    }
    
    void goster() {
        System.out.print("Ata:" + sayi + " ");
    }
}

class Torun extends Ata {
    int sayi = 2;
    
    {
        sayi = 3;
        System.out.print("Blok:" + sayi + " ");
    }
    
    Torun() {
        System.out.print("Torun:" + sayi + " ");
    }
    
    @Override
    void goster() {
        System.out.print("Goster:" + sayi + " ");
    }
}

public class SinavSoru1 {
    public static void main(String[] args) {
        Ata obj = new Torun();
        System.out.print("Son:" + obj.sayi + " ");
        // Goster: 0, Blok: 3, Torun: 3, Son: 1
    }
}
```

### Örnek 14
```java
public class SinavSoru2 {
    
    @SuppressWarnings("finally")
    static String test() {
        String sonuc = "";
        
        for (int i = 0; i < 3; i++) {
            try {
                if (i == 1) 
                    throw new RuntimeException();
                sonuc += i;
                continue;
            } catch (Exception e) {
                sonuc += "H";
                return sonuc; 
            } finally {
                sonuc += "F";
                if (i == 1) 
                    continue; // Bu continue satırı işlendiğinde o sırada catch içinde return bekliyor olsa bile o return iptal olur. 
            }
        }
        return sonuc + "Bitti";
    }

    public static void main(String[] args) {
        System.out.print(test());
        // 0FHF2F
    }
}
```

### Örnek 15
```java
class Kapsul {
    int deger;
    Kapsul(int d) { this.deger = d; }
}

public class SinavSoru3 {
    public static void main(String[] args) {
        Kapsul[] dizi1 = { new Kapsul(10), new Kapsul(20) };
        
        Kapsul[] dizi2 = dizi1.clone();
        // Hata atmaz, Kapsul sınıfı clone override etmemiş olsa bile java'da array'lerin her zaman bunu yapabilme izinleri vardır.
        
        dizi2[0].deger = 99;
        
        dizi2[1] = new Kapsul(55);
        
        System.out.print(dizi1[0].deger + " ");
        // 99
        System.out.print(dizi1[1].deger + " ");
        // 20
        System.out.print(dizi2[0].deger + " ");
        // 99
        System.out.print(dizi2[1].deger + " ");
        // 55
    }
}
```

### Örnek 16
```java
class Kedi {
    String ad;
    Kedi(String ad) { this.ad = ad; }
}

public class SinavSoru1 {
    static void degistir(Kedi k1, Kedi k2) {
        k1.ad = "Tekir";
        
        k2 = new Kedi("Pamuk");
        k2.ad = "Minnoş";
    }

    public static void main(String[] args) {
        Kedi x = new Kedi("Sokak");
        Kedi y = new Kedi("Ev");

        degistir(x, y);

        System.out.print(x.ad + " ");
        System.out.print(y.ad + " ");
    }
    // Output Tekir ve Ev olur çünkü Java pass-by-value çalıştığı için fonksiyona bir kopyasını gönderir yani klonunu. Bu klonla aynı adresi paylaştığından içerideki değişiklik dışarıdakini de etkilemektedir tabii ki. Ancak y değişmez çünkü ona içerde yeni bir referans tanımlandı ancak kopyası gelen y değişleni hala aynı dolayısı ile o değişmedi.
}
```

### Örnek 17
```java
interface Gizli {
    void calis();
}

public class SinavSoru2 {
    int x = 1;

    Gizli uret() {
        int y = 2;
        
        return new Gizli() {
            int x = 5;
            
            @Override
            public void calis() {
                y++; 
                System.out.print(x + " ");
                // 5
                System.out.print(SinavSoru2.this.x + " ");
                // 10
                System.out.print(y + " ");
                // Bir anonymous class veya local inner class, dışındaki metodun local değişkenlerini değiştiremez. Yalnızca okuyabilir dolayısı ile bu satır hata atacaktır.
            }
        };
    }

    public static void main(String[] args) {
        SinavSoru2 s = new SinavSoru2();
        Gizli g = s.uret();
        
        s.x = 10;
        
        g.calis();
    }
}
```

### Örnek 18
```java
interface IYetenek {
    default void goster() {
        System.out.print("Yetenek ");
    }
}

abstract class TemelNesne {
    public void goster() {
        System.out.print("Temel ");
    }
}

class Kahraman extends TemelNesne implements IYetenek {
    
}

public class SinavSoru3 {
    public static void main(String[] args) {
        Kahraman k = new Kahraman();
        k.goster();
        // Temel, CLASS ALWAYS BEATS THE FUCK UP ALL DA INTERFACESA
        
        IYetenek y = new Kahraman();
        y.goster();
        // Temel, Polymorphism ile tanımlansa bile hangisnin override edildiği yine sınıfa göre kararı alınır.
    }
}
```

### Örnek 19
```java
abstract class B{
    public void m() {
        System.out.println("b");
    }
    static void m2(){
        System.out.println("b_static");
    }
}
class C extends B{
    public void m() {
        System.out.println("c");
    }
}
public class Main{
    public static void main(String [] args) {
        new B().m();
        // HATA ATAR, çünkü abstract bir class new B() ile oluşturulamaz.
        new C().m();

        B.m2();
    }
}
```

### Örnek 20
```java
public class M{
    public static void main(String[] args) {
        new test().A();
    }
}
class test{
    int A() throws Throwable{
        try{
            System.out.println(1);
            B();
            System.out.println(2);
        }
        catch(IOException e){
            System.out.println(3);
            return -1;
        }
        catch (Exception e){
            System.out.println(4);
            return -1;
        }
        catch (Throwable e) {
            System.out.println(5);
            return -1;
        }
        finally {
            System.out.println(6);
        }
        System.out.println(7);
        return 1;
    }
    void B() throws Throwable{
        try{
            System.out.println(8);
            if(2 <3) 
                throw new FileNotFoundException();
            System.out.println(9);
        }catch(IOException e){
            System.out.println("a");
            C();
            System.out.println("b");
        }
        catch(Exception e){
            System.out.println("c");
            throw new IOException();
        }
        System.out.println("d");
    }
    void C() throws Throwable{
        try {
            System.out.println("e");
            throw new Throwable();
        }
        finally{
            System.out.println("f");
        }
    }   
}

//output: 18aef56, return -1
```   
### Örnek 21
```java
public class M{
    public M() {
        System.out.println("o");
    }
    void m(){
        System.out.println("m");
    }
    class Inner extends M{
        void n() {
            m();
        }
        public Inner() {
            n();
            System.out.println("i");
        }
    }
    public static void main(String [] args){
        new M().new Inner();
    }
}
// output: oomi
```

### Örnek 22
```java
class A2{
    public boolean equals(A2 a) {
        System.out.println("equals(A2 a) calisti.");
        System.out.println("this: " + this);
        System.out.println("a: " + a);
        return false;
    }
    public String toString() {
        return "A";
    }
}

public class M {
    public static void main(String[] args) {
        A2 a = new A2();
        Object obj = a;
        System.out.println(obj.equals(a));
        System.out.println(a.equals(obj));
        System.out.println(a.equals(a));
        System.out.println(obj);
    }
}
// equals metodu burada override edilmemiştir çünkü orijinalde içine parametre olarak Object türünde bir parametre alır. Bu nedenle, A2 sınıfında tanımlanan equals metodu, Object sınıfının equals metodunu geçersiz kılmaz.
//ilk satırda objenin equals metodu çağrılır. obj bir Object sınıfından olduğu için ve override edilmediği için obj'nin equals metodu çalışır ve bu da Object sınıfının equals metodunu çağırır. Object sınıfının equals metodu, iki nesnenin referanslarının aynı olup olmadığını kontrol eder. Bu durumda, true döner adresleri aynı çünkü.
//ikinci satırda ise a'nın equals'ı çağırılır ancak içine aldığı obj bir Object olduğu için ve Object'i ilk kabul edecek olan equals metodu Object sınıfına ait olduğu için Object sınıfının equals metodu çalışır ve bu da true döner adresleri aynı.
//üçüncü satırda a'nın equals'ı çağırılır ve içine aldığı a bir A2 türünde olduğu için ve A2 sınıfında equals metodu tanımlandığı için A2 sınıfının equals metodu çalışır ve bu da false döner çünkü A2 sınıfının equals metodu her zaman false döndürür.
//son satırda ise toString sınıfı equals'ın aksine override edilmiştir ve A2 sınıfının toString metodu çağrılır. Bu da "A" döner.
```

### Örnek 23
```java
class Motor {
    int guc;
    String tip;
    
    Motor(int guc, String tip) { 
        this.guc = guc; 
        this.tip = tip; 
    }
    public String toString() { 
        return guc + " " + tip; 
    }
}

class Araba implements Cloneable {
    int yil;
    Motor motor;

    Araba(int yil, Motor motor) {
        this.yil = yil;
        this.motor = motor;
    }

    public Object clone() {
        try {
            return super.clone();
            // Sığ Kopyalama (Shallow Copy)
        } catch (Exception e) { 
            return null; 
        }
    }

    public String toString() {
        return yil + " " + motor;
    }
}

public class M {
    
    static void modifiyeEt(Araba a, Motor yeniMotor) {
        a.motor = yeniMotor;
        a.yil = 2025;
    }

    public static void main(String[] args) {
        Motor m1 = new Motor(100, "Dizel");
        Araba araba1 = new Araba(2010, m1);
        
        System.out.println("Başlangıç Durumu:");
        System.out.println("Araba1: " + araba1);

        // Klonlama İşlemi
        Araba araba2 = (Araba) araba1.clone();

        System.out.println("\nKlonlama Sonrası Durum:");
        System.out.println("Araba1: " + araba1);
        System.out.println("Araba2: " + araba2);

        // Araba 2 üzerinden yapılan değişiklikler
        araba2.motor.guc = 200;
        // araba2'nin motor gücünü değiştirilmesi araba1'i de etkiler çünkü motor nesnesi paylaşılır
        araba2.yil = 2015;
        // araba2'nin yılını değiştirilmesi araba1'i etkilemez çünkü yıl primitive tiptir ve ayrı kopyalanır

        System.out.println("\nDeğişiklikler Sonrası Durum:");
        System.out.println("Araba1: " + araba1);
        System.out.println("Araba2: " + araba2);

        // Metot çağrısı ve yeni bir Motor
        Motor m2 = new Motor(300, "Elektrik"); 
        modifiyeEt(araba1, m2);
        // araba1'in motorunu değiştirme ve yılını güncelledikten sonra araba2'de etkilenmez çünkü araba2'nin motoru hala m1'e referans verir

        // Final Çıktıları
        System.out.println("\nMetot Çağrısı Sonrası Final Durum:");
        System.out.println("Araba1: " + araba1);
        System.out.println("Araba2: " + araba2);
    }
}
```
### Örnek 24
```java
class Kule {
    static { 
        System.out.println("Kule-Statik "); 
    }
    
    { 
        System.out.println("Kule-Blok "); 
    }
    
    Kule() { 
        System.out.println("Kule-Const "); 
    }

    // 1. Kiracı: Statik İç Sınıf (Bağımsız Ruh)
    static class Zirve {
        static { 
            System.out.println("Zirve-Statik "); 
        }
        
        { 
            System.out.println("Zirve-Blok "); 
        }
        
        Zirve() { 
            System.out.println("Zirve-Const "); 
        }
    }

    // 2. Kiracı: Normal İç Sınıf (Ev Sahibine Muhtaç)
    class Zemin {
        static {
            System.out.println("Zemin-Statik ");
        }
        { 
            System.out.println("Zemin-Blok "); 
        }
        
        Zemin() { 
            System.out.println("Zemin-Const "); 
        }
    }
}

public class M {
    public static void main(String[] args) {
        System.out.println("Basladi ");
        
        Kule.Zirve z1 = new Kule.Zirve();
        // Zirve statik, zirve instance, zirve const
        
        Kule k1 = new Kule();
        // kule statik, kule blok, kule const
        
        Kule.Zemin z2 = k1.new Zemin();
        //  zemin statik, zemin blok, zemin const

        Kule.Zemin z3 = k1.new Zemin();
        // zemin blok , zemin const
    }
}
```

### Örnek 25
```java
class Person implements Cloneable{
    String name;
    int age;
    Phone phone;
    Person(String name, int age, Phone phone){
        this.name = name;
        this.age = age;
        this.phone = phone;
    }
    public String toString(){
        return "Name: " + name + ", Age: " + age + ", Phone: " + phone;
    }
    protected Person clone() throws CloneNotSupportedException{
        return (Person) super.clone();
    }
}

class Phone{
    String brand;
    int model;
    Phone(String brand, int model){
        this.brand = brand;
        this.model = model;
    }
    public String toString(){
        return "Brand: " + brand + ", Phone Model: " + model;
    }
}
public class M {
    public static void main(String[] args) {
        Person p1 = new Person("Alice", 30, new Phone("Apple", 15));
        
        System.out.println("Person 1: " + p1);
        System.out.println("------------------------------");

        Person p3 = p1;
        //p3.name = "Charlie";
        p3.name = new String("Charlie");    // bu satır da üstteki satır da her türlü p1'i etkiler çünkü p3 ve p1 aynı nesneyi referans ediyorlar.
        p3.age = 35;
        p3.phone.brand = "Google";
        p3.phone.model = 10;    // bu yapılan işlem her şeyi değiştirerek p1'i de etkiler çünkü p3 ve p1 aynı nesneyi referans ediyorlar.

        System.out.println("p3 = p1 dedikten sonra:");
        System.out.println("Person 1: " + p1);
        System.out.println("Person 3: " + p3);
        System.out.println("------------------------------\n p1'e yeni bir telefon objesi atandıktan sonra durum:");
        p1.phone = new Phone("Samsung", 20);    // bu işlem p3'ü de etkiler çünkü p3 ve p1 aynı nesneyi referans ediyorlar.
        System.out.println("Person 1: " + p1);
        System.out.println("Person 3: " + p3);
        System.out.println("------------------------------");
        System.out.println("------------------------------");



        p3=null;
        p1 = new Person("Alice", 30, new Phone("Apple", 15));

        System.out.println("p3=null ve p1 eski haline atandıktan sonra vee p3=p1.clone() dedikten sonra:");
        try{
            p3 = p1.clone();
            //p3.name = "Charlie";
            p3.name = new String("Charlie");    // bu satır da üstteki satır da her türlü p1'i etkilemez çünkü p3 ve p1 farklı nesneleri referans ediyorlar.
            p3.age = 35;
            p3.phone.brand = "Google";
            p3.phone.model = 10;
            
            System.out.println("Person 1: " + p1);
            System.out.println("Person 3: " + p3);
            System.out.println("------------------------------\n p1'e yeni bir telefon objesi atandıktan sonra durum:");
            p1.phone = new Phone("Samsung", 20);    // bu işlem p3'ü etkilemez çünkü p3 ve p1 farklı nesneleri referans ediyorlar.
            System.out.println("Person 1: " + p1);
            System.out.println("Person 3: " + p3);
            System.out.println("------------------------------");
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```

### Örnek 26
```java
class Outer {
    Outer() { 
        System.out.println("1- Outer (Ev Sahibi) Constructor Çalıştı"); 
    }

    class Inner {
        Inner() { 
            System.out.println("2- Inner (Kiracı) Constructor Çalıştı"); 
        }
    }
    static class StaticInner{
        StaticInner(){
            System.out.println("3- Static Inner Const Çalıştı");
        }
    }
}

class Main {
    public static void main(String[] args) {
        Outer evSahibi = new Outer(); 
        // Çıktı: "1- Outer (Ev Sahibi) Constructor Çalıştı"

        Outer.Inner kiraci = evSahibi.new Inner(); 
        // Çıktı: "2- Inner (Kiracı) Constructor Çalıştı"
        
        Outer.Inner kiraci2 = new Outer().new Inner();
        // Çıktı: "1- Outer (Ev Sahibi) Constructor Çalıştı" ve "2- Inner (Kiracı) Constructor Çalıştı"

        Outer.StaticInner kiraciStatic = new Outer.StaticInner();
        // Çıktı: "3- Static Inner Const Çalıştı". new Outer() dememize rağmen Outer consturctor method yine de tetiklenmedi.
    }
}
```

## File I/O

**FileOutputStream:** File'a yazmak için kullanılacak olan output stream.

**PrintWriter:** Output stream'e yine yazı reprensete etmek için.

**Flush:** Buffer'da tutulan herhangi data'nın direkt olarak output file'a yazılmasını sağlar. Flush olmadan buffered data memory'de kalacaktır ve programla birlikte kaybolacaktır.

FileOutputStream creates a new file or overwrite an existing one. Oluşturulacak olan PrintWriter nesnesi FileOutputStream'a aktarılır argüman olarak.
- `public FileOutputStream(String name) throws FileNotFoundException`
- `public FileOutputStream(File file) throws FileNotFoundException` 

şeklinde body'si atılır. Bu print, println, printf gibi metotlar da bu PrintWriter'dan gelmektedir.

-----
**Scanner:** Primitive type ve string'leri input olarak okunması için kullanılır, file'lardan da tabii.

**BufferedReader:** Character-input stream'den text okumak için kullanılır. `read() ve readLine()` gibi okuma fonksiyonları barındırır.

- **read():** Input stream'den tek karakter okur ve integer value olarak return eder, stream'ın sonunda ise -1 return eder.
- **readLine():** Text line olarak okur ve String return eder, stream'ın sonunda ise null return eder.

Input'un bitip bitmediğini öğrenmek için Scanner sınıfının `hasNext(), hasNextBoolean(), hasNextDouble(), hasNextInt(), hasNextLine(),` metotları kullanılabilir.


**Closing Streams:** Proper bir cleanup işlemi için gereklidir. Aynı zamanda içerisinde flush da çağırarak save almayı sağlar kapatmadan hemen önce.

### Örnek 1
```java
// method() ve method2() içerisinde yazma işlemi için dosyalar nasıl şekillerde açılır infosu var. Kısaca:
// PrintWriter out = new PrintWriter(new FileOutputStream("output.txt"));
// out.print bla bla
public void method(){
    PrintWriter out = null;
    try{
        out = new PrintWriter(new FileOutputStream("output.txt"));
    }catch(FileNotFoundException e){
        System.out.println("Error opening the file output.txt for writing!");
    }
    out.println("Hello world"); // dosyaya yazma
    out.close();
}
public void method2(){
    PrintWriter out = null;
    FileOutputStream file = null;
    
    try{
        file = new FileOutputStream("output.txt", true); // buradaki true argümanı append anlamına gelmektedir.
    }catch(FileNotFoundException e){
        System.out.println("Error opening the file output.txt for writing!");
        return;
    }

    try{
        out = new PrintWriter(file);
        out.println("Hello world");
    }catch(FileNotFoundException e){
        System.out.println("Error opening the file output.txt for writing!");
    }finally{
        out.close();
    } 
}
```

### Örn1k 1.5
```java
import java.io.IOException;
import java.io.PrintWriter;
import java.nio.charset.StandardCharsets;

public class TextYazici {
    public static void main(String[] args) {
        
        // 1. DİKKAT DETAYI: Try-with-resources kullandık. İş bitince out.close() otomatik çalışacak!
        // 2. DİKKAT DETAYI: StandardCharsets.UTF_8 ile Türkçe karakterlerin (ş, ğ, ç) bozulmasını engelledik.
        // 3. DİKKAT DETAYI: false parametresi "Sıfırdan yaz (Overwrite)" demektir. true yapsaydık dosyanın sonuna eklerdi (Append).
        
        try (PrintWriter out = new PrintWriter(new FileOutputStream("veriler.txt"))) {

            // Farklı veri tiplerimiz
            int yas = 25;
            double maas = 10500.50;
            boolean aktifMi = true;
            String isim = "ahmet";
            char kanGrubu = 'A';

            /*## Açıklama: println() ve toString()

            `println()` metodu, parametre olarak aldığı herhangi bir nesneyi yazdırmadan önce otomatik olarak `toString()` metodunu çağırır. Eğer nesne kendi `toString()` metodunu override etmişse, o özel implementasyon kullanılır. Eğer override etmemişse, `Object` sınıfındaki default `toString()` kullanılır (bu da genelde `ClassName@hashCode` formatında bir şey döner).

            ### Örnek:
            class WithToString {
                @Override
                public String toString() {
                    return "Bu nesnenin custom toString'i var!";
                }
            }

            class WithoutToString {
                // toString() override edilmemiş
            }

            public class Main {
                public static void main(String[] args) {
                    WithToString obj1 = new WithToString();
                    WithoutToString obj2 = new WithoutToString();
                    
                    System.out.println(obj1); 
                    // Çıktı: "Bu nesnenin custom toString'i var!"
                    
                    System.out.println(obj2); 
                    // Çıktı: "WithoutToString@15db9742" (default toString)
                }
            }

            **Kısaca:** Dosyaya yazılacak olan, hangi nesnenin `toString()`'i override edilmişse o yazılır, edilmemişse default format yazılır.
            */

            out.println("--- calisan bilgileri ---");
            out.println("İsim: " + isim);          // String yazdı
            out.println("Yas: " + yas);            // int'i "25" metnine çevirip yazdı
            out.println("Maas: " + maas);          // double'ı "10500.50" metnine çevirip yazdı
            out.println("Kan Grubu: " + kanGrubu); // char yazdı
            out.println("calisiyor mu: " + aktifMi); // boolean'ı "true" metnine çevirip yazdı

        } catch (IOException e) {
            System.out.println("Dosyaya yazarken kaza çıktı: " + e.getMessage());
        }
    }
}
```

### Örnek 2
```java
// readFromFile() ve readFromFileBufferedReader() içerisinde okuma işlemi için dosyalar nasıl şekillerde açılır infosu var. Kısaca:
// Scanner scanner = new Scanner(new FileInputStream(filename));
// veya
// BufferedReader in = new BufferedReader(new FileReader(filename));
// scanner.next bla bla veya in.read bla bla

// ÖNEMLİ: Scanner sınıfı hayvan gibi içine FileInputStream de alır FileReader de alır her türlü okur. Ama BufferedReader'ın içine Scanner'in aldığı gibi FileInputStream sınıfını veremezsin çünkü BufferedReader adı üstünde reader olduğu için kelime harf bakar bizim FileInputStream ise stream şeklinde 0 ve 1 byte'lar okuyarak işlemi yapar.
static void readFromFile(String filename){
    try{
        Scanner scanner = new Scanner(new FileInputStream(filename));
        System.out.println(scanner.nextInt());
        /**
         * nextInt();
         * nextDouble();
         * nextFloat();
         * nextLong();
         * nextShort();
         * next();  // tek kelime string value okur
         * nextLine();  // multiple word string okunur
         * 
         * Şöyle güzel bir durum var ki, tek tek hepsi nasıl hata atar diye düşünmeye gerek yok, kendi okuyabileceği belirli tür harici bir şey okunmaya çalışılınırsa InputMismatchException atar ve pointer da ilerlemez while ile bulana kadar ilerleteyim taktiği işe yaramaz :D. 
        */
        scanner.close();
    }catch(FileNotFoundException e){
        System.out.println("Error occurred while trying to read from the file: " + e.getMessage());
    }
}
static readFromFileBufferedReader(String filename) {
    try{
        BufferedReader in = new BufferedReader(new FileReader(filename));
        String line;

        ```java
        while((ch = in.read()) != null)
            System.out.print((char)ch);
        while((line = in.readLine()) != null)
            System.out.print(line);

        // Diğer read metotları:
        // read(): Tek karakter okur, integer döner (-1 ise stream bitti)
        // readLine(): Bir satır okur, String döner (null ise stream bitti)
        // readUTF(): UTF formatında string okur (binary stream'lerden)
        // readInt(), readDouble(), readBoolean() vb.: Primitive type'ları okur (binary)
        ```
        /*
        **read() Metodu Ne İşe Yarıyor?**
        - Stream'den tek bir byte okur
        - Integer değer döner (0-255 arası)
        - Stream sonuna ulaşırsa -1 döner
        - Genelde sıkıntılı çünkü int döner, char'a cast gerekir

        **readLine() Metodu Ne İşe Yarıyor?**
        - Stream'den tüm bir satırı okur
        - String olarak döner
        - Stream sonuna ulaşırsa null döner
        - read()'den daha uygun çünkü direkt String döner

        **Hangisi Daha İyi?**
        - Metin dosyaları için: `readLine()` (daha temiz, satır satır işler)
        - İkili dosyalar için: `read()` veya `readInt()` vb. (byte seviyesi kontrol gerekiyorsa)
        - Hız önemliyse: `readLine()` kullanılan BufferedReader'dan öne geçer
        */

        in.close();
    }catch(FileNotFoundException e){
        System.out.println("Error occurred while trying to read from the file: " + e.getMessage());
    }catch(IOExcetion e){
        System.out.println("Error occurred while trying to read from the file: " + e.getMessage());
    }
}
```

### Örnek 3
```java
// normalde System.out ve System.err konsol çıktılarında görülürken setOut() ve setErr() ile bir yere redirect edebiliriz.
public void method(){
    Scanner scanner = new Scanner(System.in);
    System.out.println("Enter number:");
    int number = scanner.nextInt();
    System.out.println("You entered: " + number);

    try{
        System.setOut(new PrintStream(new FileOutputStream("output.txt")));
    }catch(FileNotFoundException e){
        System.out.println("Error opening the file output.txt for writing!");
        return;
    }
    try{
        System.setErr(new PrintStream(new FileOutputStream("error.txt")));
    }catch(FileNotFoundException e){
        System.out.println("Error opening the file error.txt for writing!");
        return;
    }
    System.out.println("Write to output.txt");
    System.err.println("Write to error.txt");
    scanner.close();
}
```

File sınfının birçok özelliği bulunmaktadır. Directory path'leri ve yine ona özel fonksiyonlar attribute'ler sağlar.

**Absolute Path**: Root'tan hedefe giden path'in tamamı.

**Relative Path**: Current path'tan hedefe giden path'in tamamı.

**File Name:** Adı üstünde. 

**Path Seperator:** Windows'ta '\\\\', Unix'te '/'

### Örnek 4
```java
public static void main(String[] args){
    File absolutePath = new File("C:/path/to/the/example.txt");
    System.out.println(absolutePath.getAbsolutePath()); // C:/path/to/the/example.txt
    File absolutePath = new File("/to/the/example.txt"); // /to ile to arasında fark yok.
    System.out.println(absolutePath.getAbsolutePath()); // C:/path/to/the/example.txt
    File relativePath = new File("the/example.txt"); 
    System.out.println(relativePath.getAbsolutePath()); // C:/path/to/the/example.txt

    File filePath = new File("application.log");
    System.out.println("File name: " + filePath.getAbsolutePath()); // C:/to/your/current/path/application.log
}
```
### Örnek 5
```java
public static void main(String[] args){
    File file1 = new File1("example.txt");
    System.out.println("File 1 path: " + file1.getAbsolutePath());

    File childFile = new File("/path/to/parent/", "example.txt");   // buradaki ilk argüman parent path, ikinci argüman ise child file ismi.
    System.out.println("Child file path: " + childFile.getAbsolutePath());

    File parentDir2 = new File("/path/to/parent");
    File childFile2 = new File(parentDir2, "examples.txt");
    System.out.println("Child file 2 path: " + childFile2.getAbsolutePath());   // cevabın dönülmesi için dosyanın gerçekten var olmasına gerek yok :D burda oluşturlduğu path durumu neyse o şekilde dönülür exists(), length(), isFile(), isDirectory(), lastModified(), canRead(), canWrite() gibi metotlar da anyı şekilde gerçekte var olmayan file için kullanılabilir.
}

// File information methods: exists(), canRead(), canWrite(), isDirectory(), isFile(), getPath(), getAbsolutePath(), getParent(), length(), lastModified()
```

### Örnek 6
```java
// File manipulation methods: mkdir(), delete(), list(), renameTo(String s), createNewFile()

public void directoryManipulationExample(){
    File directory = new File("exampleDirectory");
    System.out.println("Directory created: " + directory.mkdir());  // true, mkdir() dümdüz verdiğin path'te directory açar, mkdirs() ile eğer verdiğin path nested ise hepsini gider tek tek açar. illa directory değil file lazım diyosan tek çözümü .createNewFile()
    System.out.println("Directory deleted: " + directory.delete());

    directory = new File("Lecture/p1");     //eğer yukardaki silme işlemi yapılmadan direkt burada değiştirilseydi directory'nin point ettiği path değişeceği için artık işlemler buradan devam edilecek olacaktı
    System.out.println("Contents of directory: ");
    String[] contents = directory.list();
    for(String content : contents)
        System.out.println(content);
}
public void fileManipulationExample(){
    File oldFile = new File("oldFile.txt");
    File newFile = new File("newFile.txt");
    File deleteFile = new File("deleteFile.txt");

    System.out.println("File renamed: " + oldFile.renameTo(newFile)); // eğer bu dosyalar path'te mevcutsa bu işlem yapılıktan sonra manipüle etmek istersen false döner hiçbir şey yapmaz. linux'te override eder geçer tabi :D.
    System.out.println("File deleted: " + newFile.delete());
    System.out.println("File created: " + oldFile.createNewFile());
    deleteFile.createNewFile();
    deleteFile.deleteOnExit();      //jvm kapandığı anda dosyayı siler geçer.
}
```

Binary dosyaların oluşumunda 

- ObjectInputStream
- ObjectOutputStream

kullanılır; human-readable değildir, efficient storage, faster processing.

### Örnek 7
```java
public static void writeInts(int[] numbers, String fileName){       // fileName: numbers.bin
    try{
        ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream(fileName));
        for (int num: numbers)
            out.writeInt(num);
        // Methods
        out.writeBoolean(true);
        out.writeByte(127);
        out.writeChar('A');     //mesela gidip çok alakasız 2 integer değer versen direkt aolarak incompatible types hatası atar geçer
        out.writeDouble(3.14);      //ancak bu durumda gidip de 3 integer değer verirsen kabul edilir çünkü kolayca cast edilebilecek bir türdür ve compiler onu direkt 3.0 olarak değiştirip double'a cast eder ve yazar.
        out.writeFloat(1.11f);
        out.writeInt(100);  // ama gidip de buna 100.14 dersen iş karışır, compiler error atar
        out.writeLong(10000000000);
        out.writeShort(23767);
        out.close();
    }catch(IOException e){
        e.printStackTrace();
    }
}

public static void readInts(String fileName){
    int[] numbers = null;
    try{
        ObjectInputStream in = new ObjectInputStream(new FileInputStream(fileName));
        int length = in.available() / 4;    //available -> okunmayı beklenen verinin total byte boyutu, 4'e bölmenin sebebi de integer tutuyor olması ve convert edilecek olmasıdır.
        int[] numbers = new int[length];
        for(int i=0; i<length; i++)
            numbers[i] = in.readInt();
        /*
        cast edip length bulmaktansa aşağıdaki available() çzöümü de kullanılabilir.

        int length = 0;
        while(in.available() > 0){
            in.readInt();
            length++;
        }
        in = new ObjectInputStream(new FileInputStream(fileName));  // dosya pointer'ını başa döndürmek için baştan tanımlandı
        int[] numbers = new int[length];
        for(int i=0; i<length; i++)
            numbers[i] = in.readInt();
        */
        in.close();
    }catch(IOException e){
        e.printStackTrace();
    }
    return numbers;
    // Methods:
    /**
     * aynı yukarıda da bahsedildiği gibi çok alakasız tür okunmaya çalışırsa okuyucunun haricinde bu sefer hata ATILMAZ, ancka ortaya salak bi sonuç döner kendince kendi türüne cast edip yazdırır
     * 
     * in.readBoolean();
     * in.readByte();
     * in.readChar();
     * in.readDouble(); int sayı'ya eşitlemek istersen compile olmaz, ha okuyup(int) diye cast edersen olur ancak daraltamaz okurken. double sayıyı readInt ile okumak en salakçası olur zaten imleç kafayı yer
     * in.readFloat();
     * in.readInt();    mesela double'a atanacak ise ve okuduğu 10 integer değeri genişleterek 10.0 olarak double değişekene atayabilir. yine int sayıyı readDouble ile okumak kod gümler direkt hangi bytlearı okuyacağınıu bilemeyeceğinden
     * in.readLong();
     * in.readShort();
    */
}

public static void readInts2(String fileName){
    ArrayList<Integer> numbers = new ArrayList<>();
    try{
        ObjectInputStream in = new ObjectInputStream(new FileInputStream(fileName));
        while(true){
            try{
                numbers.add(in.readInt());  // array örneğindeki gibi cast etmeye gerek yok arraylist integer tutması ile gelen veriyi esnetip oynarak kendine uygun hale getirebiliyor.
            }catch(EOFException e){
                break;
            }
            in.close();
        }
    }catch(IOException e){
        e.printStackTrace();
    }
    return numbers;
}
```

Dosyaya obje yazabilmek için `Serializable` interface'i kullanılır. `class Person implements Serializable` şeklindedir ve `writeObject(Object o)` /  `readObject()` şeklinde kullanılabilir.

### Örnek 8
```java
class Employee implements Serializable{
    String name;
    Date birthDate;

    Employee(String name, Date birthDate){
        this.name = name;
        this.birthDate = birthDate;
    }
}
class Date implements Serializable{ // yazılan sınıf employee olsa da buna da implements lazım yoksa dışarıda kalır emnployee ile birlikte dosyaya yazılabilme iznine sahip olmaz :D
    Date(){
        // set current date
    }
}

public static void main(String[] args){
    Date birthDate = new Date();

    Employee employee = new Employee("John Doe", birthDate);

    // WRITE
    try(ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("employee.bin"))){     // file oluşturma işlemleri try içinde bu şekilde yapılabiliyor, bu şekilde close'e gerek kalmadan kendi işi bitince kapatmaktadır finally'de falan da halletmeye gerek kalmadan. 
        out.writeObject(employee);
        // çok güzel alakasız bir detay: eğer static bir değişkenimiz olsaydı ve dosyaya birkaç obje yazıldıktan sonra o değer değiştirilseydi ve okunsaydı DEĞİŞMİŞ haliyle okunurdu, static bir değişken olduğu için zaten dosyaya hiç yazılmamış oluyor, okunurken de koddan okunuyor static değişken
        // başka bir durum olsun extends durumunda base class implement etmesin ve child class etsin, yazma konusunda hiç sıkıntı olmaz ancak okunurken baba serialized olmadığı için onun değerleri boş okunur çünkü base class'ı bilmiyo, sonra da gider base class'ın boş constructor'unu çağırır.
    }catch(IOException e){
        e.printStackTrace();
    }

    // READ
    try(ObjectInputStream in = new ObjectInputStream(new FileInputStream("employee.bin"))){
        Employee e = (Employee)in.readObject();
        System.out.println("Name: " + e.name);
        System.out.println("Birth Date: " + e.birthDate);
    }catch(ClassNotFoundException e){
        e.printStackTrace();
    }catch(IOException e){
        e.printStackTrace();
    }

    // WRITE ARRAY
    Employee[] employees = {new Employee("Alice", birthDate), new Employee("Alice", birthDate), new Employee("Alice", birthDate)};
    try(ObjectOutputStream out = new ObjectOutputStream(new FileOutputStream("employees.bin"))){     // file oluşturma işlemleri try içinde bu şekilde yapılabiliyor, bu şekilde close'e gerek kalmadan kendi işi bitince kapatmaktadır finally'de falan da halletmeye gerek kalmadan. 
        out.writeObject(employees); // array formatında yazılmakta, n adet employee diye yazmıyor, n boyutunda employee array'i diye yazılmakta. 
    }catch(IOException e){
        e.printStackTrace();
    }

    // READ ARRAY
    try(ObjectInputStream in = new ObjectInputStream(new FileInputStream("employee.bin"))){
        Employee[] employeeArray = (Employee[])in.readObject(); //nasıl array yazdıysak array olarak da okuyabilriiz.
        System.out.println("Employee'ler okundu:");
        for(Employee e : employeeArray){
            System.out.println("Name: " + e.name);
            System.out.println("Birth Date: " + e.birthDate);
        }
        
    }catch(ClassNotFoundException e){
        e.printStackTrace();
    }catch(IOException e){
        e.printStackTrace();
    }
}
```

**Random Access File**, Java'da bir sınıf olarak binary file'lar üzerinde access operation'lar yapabilmemizi sağlar. Diğer okuma yazmaların aksine istediği pozisyonda işlem yapabilir. `RandomAccessFile(File file, String mode)` şeklinde tanımlanır. Buradaki mod seçiminde "r" yazma, "rw" yazma ve okuma olarak tanımlanabilir.

- **getFilePointer():** Current position döner.
- **length:** Adı üstünde.
- **seek(long pos):** File pointer'ı hareket ettirir.
- write/read methods e.g. readInt, writeInt

### Örnek 9
```java
RandomAccessFile file = new RandomAccessFile("randomAccess.bin", "rw");
file.writeInt(1);
file.writeDouble(3.14);
file.writeBoolean(true);
file.writeUTF("ABC");
file.writeByte(65);

file.seek(0); // dosyanın başına git
int intValue = file.readInt();
System.out.println("read int value: " + intValue);

file.seek(4); // 4 byte ilerlet, çünkü integer okuduk
double doubleValue = file.readDouble();
System.out.println("read double value: " + doubleValue);

file.seek(12); // 8 byte ilerlet, çünkü double okuduk
boolean booleanValue = file.readBoolean();
System.out.println("read boolean value: " + booleanValue);

file.seek(13); // 1 byte ilerlet, çünkü boolean okuduk
String stringValue = file.readUTF();
System.out.println("read string value: " + stringValue);

file.seek(13 + stringValue.length() + 2); // length kadar + 2 ilerlet, 2'nin sebebi ise string'lerde otomatik eklenen 2byte'lık başlık kısmının da atlanması için
byte byteValue = file.readByte();
System.out.println("read byte value: " + byteValue);

System.out.println("file length: " + file.length());    // çıktı 29 gelir, pos olarak 28'deydik fazladan gelen 1 ise sonda okunan byte değişkeninden gelmekte.

file.close();

try{
    RandomAccessFile file = new RandomAccessFile("randomAccess.bin", "rw");
    file.seek(12); // boolean değerin olduğu kısma gittik.
    file.writeBoolean(false); // oradaki değer anlık olarak değişti.
    file.close();
}catch(IOException e){
    e.printStackTrace();
}
```

### Örnek 10 - DB
```java
import java.io.*;

class Employee {
    private int id;
    private String name;
    private double salary;
    private static final int RECORD_SIZE = 50; // Assuming each record is 50 bytes

    public Employee(int id, String name, double salary) {
        this.id = id;
        this.name = name;
        this.salary = salary;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public double getSalary() {
        return salary;
    }

    public static int getRecordSize() {
        return RECORD_SIZE;
    }

    @Override
    public String toString() {
        return "Employee{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", salary=" + salary +
                '}';
    }
}

public class Main {
    private static void writeEmployeeToFile(RandomAccessFile file, Employee employee) throws IOException {
        // Find the next record point after end of file and write the employee details
        if (file.length() == 0) {
            file.seek(0);
        } else {
            int recordNumber = (int) (file.length() / Employee.getRecordSize());
            file.seek((recordNumber + 1) * Employee.getRecordSize());
        }
        file.writeInt(employee.getId());
        file.writeUTF(employee.getName());
        file.writeDouble(employee.getSalary());
    }

    private static Employee readEmployeeFromFile(RandomAccessFile file, int recordNumber) throws IOException {
        file.seek(recordNumber * Employee.getRecordSize());
        int id = file.readInt();
        String name = file.readUTF();
        double salary = file.readDouble();
        return new Employee(id, name, salary);
    }
    private static Employee[] readAllEmployeeFromFile(RandomAccessFile file) throws Exception{
        long fileLength = file.length();
        int recordCount = (int) (fileLength / Employee.getRecordSize());
        Employee[] employees = new Employee[recordCount];
        file.seek(0);
        for (int i = 0; i < recordCount; i++) {
            file.seek(i * Employee.getRecordSize());
            int id = file.readInt();
            String name = file.readUTF();
            double salary = file.readDouble();
            employees[i] = new Employee(id, name, salary);
        }
        return employees;
    }

    static void fillDatabase() {
        Employee[] employees = {
                new Employee(101, "John Doe", 50000),
                new Employee(102, "Jane Smith", 60000),
                new Employee(103, "Mehmet Yılmaz", 70000),
                new Employee(104, "Emily Brown", 55000)
        };

        try (RandomAccessFile file = new RandomAccessFile("employees.dat", "rw")) {
            for (Employee employee : employees) {
                writeEmployeeToFile(file, employee);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        // Create a new file and write some employees to it
        try (RandomAccessFile file = new RandomAccessFile("employees.dat", "rw")) {
            file.setLength(0);
        } catch (IOException e) {
            e.printStackTrace();
        }
        // fill the database with some employees
        fillDatabase();
        System.out.println("Database has been filled with some employees.");
        // get record number from user until user enters -1 to quit and display the
        // employee details
        try (RandomAccessFile file = new RandomAccessFile("employees.dat", "r")) {
            BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
            int recordNumber;
            while (true) {
                System.out.print("Enter a record number (0-3) or -1 to quit: ");
                recordNumber = Integer.parseInt(reader.readLine());
                if (recordNumber == -1) {
                    break;
                }
                if (recordNumber < 0 || recordNumber > 3) {
                    System.out.println("Invalid record number. Please try again.");
                    continue;
                }
                Employee employee = readEmployeeFromFile(file, recordNumber);
                System.out.println(employee);
            }

            // Tüm record'ları çekmek için
            System.out.println("Employee list: ");
            try{
                Employee[] emps = readAllEmployeeFromFile(file);
                for(Employee e : emps)
                    System.out.println(e);
            }catch(Exception e){
                e.printStackTrace();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

### Karışık Örnekler - Kod Trace
```java
// dosya içeriği: 123 merhaba 456
Scanner sc = new Scanner(new FileInputStream("input.txt"));
System.out.print(sc.nextInt() + " ");
System.out.print(sc.next() + " ");
if(sc.hasNextInt()) {
    System.out.print(sc.nextInt());
} else {
    System.out.print("Sayı Değil!");
}
sc.close();
// output: 123 Merhaba 456
// Scanner sınıfı (nextInt, next, nextDouble vb.) varsayılan olarak boşlukları (space, tab, enter) "ayraç" (delimiter) olarak görür ve onları otomatik olarak atlar. sc.next() gelidğinde o an spcae görmesine rağmen atlayıp kelimeyi okur. 
```

`
RandomAccessFile ile bir dosyaya writeUTF("ABC") yazdın. Hemen ardından getFilePointer() metodunu çağırdın. İmleç (pointer) hangi index'te durur?

Cevap: String başında daima 2 byte'lık header bilgisi tutulur, string length = 3. Öyleyse 3 + 2 = 5
`

```java
// dosyanın içi boş
RandomAccessFile raf = new RandomAccessFile("data.bin", "rw");
raf.seek(10); // Boş dosyanın 10. byte'ına zıpladık!
raf.writeInt(100); // Oraya bir int (4 byte) yazdık.
System.out.println(raf.length());
raf.close();
//output: 14, çünkü seek 10 yapınca baştaki 10'luk kısmı null ile doldurur. int de 4 ekleyince 14 olur length
```

`
Senaryo şu:

class Person (Serializable DEĞİL, parametresiz constructor'ı var).
class Student extends Person (Serializable EVET).

Person sınıfında String tcNo = "123" var.
Student sınıfında int okulNo = 444 var.

Bir Student objesi oluşturup TC'sini ve okul numarasını set ettin, dosyaya yazdın ve geri okudun. Okunan objenin son durumu ne olur?

// output: TC: null, OkulNo: 444, çünkü Kural: Eğer üst sınıf (Person) Serializable değilse, içindeki veriler dosyaya yazılmaz.

Okuma Anı: Java dosyadan Student'ı okur, ama tcNo verisini dosyada bulamaz. Bulamayınca Person'ın parametresiz constructor'ını çağırır.

Senaryo: Eğer tcNo değişkeni sınıfta tanımlanırken "123" atanmışsa, constructor çalışınca evet "123" olur. ANCAK, mülakatlarda bu soru genelde "TC'yi sonradan değiştirdin (mesela 999 yaptın)" şeklinde sorulur. Dosyadan okuyunca senin set ettiğin 999 değil, constructor'daki ilk değer olan null (veya varsayılan neyse o) gelir. Senin örneğinde başlangıç değeri "123" olduğu için şanslısın ama teknik olarak dosyadan okunmaz, constructor'dan yeniden yaratılır.
`

```java
// dosya:
// 42
// Merhaba

Scanner sc = new Scanner(new FileInputStream("input.txt"));
int sayi = sc.nextInt();
String satir = sc.nextLine();
System.out.println("Sayı: " + sayi + " | Satır: " + satir);
//output: Sayı: 42 | Satır: <yok> çünkü bizim nextInt() okuduktan sonra sondaki \n işaretini okumaz. gelen nextLine()  da kalan satırdan geriye kalan \n'lik kısmı okuyup bitirir ikinci satır hiç okunmaz bile :D.
```

```java
// file'da 100 ve 3.14 yazmakta
RandomAccessFile raf = new RandomAccessFile("data.bin", "r");
raf.seek(0);
double d = raf.readDouble(); 
System.out.println(d);
//output: aşırı saçma bi değer okur, scanner'da olduğu gibi cast ederek double çevirip 100.0 yazmaz bu binary dosya çünkü. 8 byte okumaya çalışır inti okuyup üstüne 3.14'ten çalar birazını
```

`
Dosyada şu an hiçbir şey yok (0 byte). Bir RandomAccessFile açtın ve sırasıyla şu operasyonları yaptın:

raf.writeUTF("XY");  (Not: writeUTF her zaman başına 2 byte "uzunluk" ekler)
raf.writeInt(100);
raf.seek(2);         // İmleci "XY" yazısının tam başına (uzunluk bilgisinden hemen sonrasına) çektin.
raf.writeChar('Z');  // Mevcut verinin üzerine 'Z' karakterini yazdın.

System.out.println(raf.length());

Bu operasyonlar sonunda dosya boyutu (length) kaç byte olur?
cevap: 8. seek(2) yapılana kadar durum <iki byte header>XY100 -> bu durumda 2 + 2 + 4 = 8 byte. sonrasında seek(2) yapıp z yazdığımızda ise <iki byte header>ZY100 olarak hacmi değişmez aynı 8 kalır.
önemli: seek(0) ve seek(1) yapılamaz. eğer yapılırsa önceden yazılan utf formatı bozularak okunmaz hale gelir. aradan bu tarz veri değiştirmek baya tehlikeli.
`

```java
FileInputStream fis = new FileInputStream("sayilar.txt"); // İçerik: "ABCDEFGHIJ"
BufferedReader br = new BufferedReader(new InputStreamReader(fis));

System.out.print((char)br.read()); // 'A' okundu
System.out.print((char)fis.read()); // 🚨 FIS üzerinden devam ettik

/*output: HATA: Java'da aynı dosyayı (stream) iki farklı sınıfla okumak teknik olarak yasak değildir, yani IOException atmaz. Ama sonuç tam bir "çorba" olur!
ANALİZ: BufferedReader'ın olayı hızdır. Sen br.read() diyerek sadece 'A' harfini istediğinde, o gider "Hazır dosyayı açmışken hepsini (veya 8KB'lık bir bloğu) belleğe çekeyim" der.

Sen br.read() ile 'A'yı alırsın ama fis'in imleci (pointer) dosyanın sonuna kadar çoktan kaymıştır bile!
fis.read() dediğinde ya hiçbir şey okuyamazsın ya da bambaşka bir byte okursun.

Özet: BufferedReader dosyayı "sömürür", arkasındaki ham stream'e bir şey bırakmaz.*/
```

```java
// 1. Yazıcı
ObjectOutputStream out1 = new ObjectOutputStream(new FileOutputStream("data.bin"));
out1.writeInt(100);
out1.close();

// 2. Yazıcı (Append modunda açtık!)
ObjectOutputStream out2 = new ObjectOutputStream(new FileOutputStream("data.bin", true));
out2.writeInt(200);
out2.close();
//output: total byte 16 olur.
// önemli: java oluşturduğu her obejctoutputstream dosyası için 4 byte header ekler.
```


## Generic

Generic yapılar, flexible bir code ortamı sağlar herhangi bir data type ile çalışabilen. Reusable olmasını sağlar. Mesela ArrayList'lerde type belirtilmelidir(primitive type tutulamaz bu arada!). `ArrayList<String> list = new ArrayList<String>()`. For-each loop yapısına uygundur listeler. ArrayList fonksiyonları:
```java
boolean add(Base_Type newElement);
void add(intindex, Base_Type newElement);

Base_Type set(int index, Base_Type newElement);
Base_Type get(int index);

Base_Type remove(int index)
void removeRange(int fromIndex, int toIndex)
boolean remove(Object theElement) 
void clear()

boolean contains(Object target) 
indexOf(Object target)
lastIndexOf(Object target)

boolean isEmpty() 
int size() 
void ensureCapacity(int newCapacity) 
void trimToSize()

Object[] toArray()
Type[] toArray(Type[] a)
Object clone() // shallowcopy
```

Bir generic yapı başka interface'leri extends edebilir(evet, implements değil extends oldu artık o). Multiple extends ilişkisi de kurulabilir & operatörü ile -> `T extends Cloneable & Number`. Eğer interface'ler sınıflar içerileceki ise class name'lerin yazılması önceliktir. Örnek:
```java
class Employee implements Cloneable{    //eğer isim String değil de T olsaydı class Employee<T extends Cloneable> implements Cloneable{} şeklinde oluşturulmalıydı, extends kısmındaki Cloneable yanına <T> almaz çünkü yalnızca beni kopyalayabilirsin demek ister. 
    String isim;

    Employee(String isim){
        this.isim = isim;
    }

    @Override
    protected Object clone() throws CloneNotSupportedException{
        return super.clone();
    }
}

class Pair1<T extends Comparable<T>>{   // ikinci gelen <T> ilk T'ye "beni yalnızca kendinle kıyasla" demek için vardır.
    T val;
    T val2;

    public T kiyasla(){
        return (val.compareTo(val2) > 0) ? val : val2;
    }
}
class Pair2<T extends Number>{
    T sayi1;
    T sayi2;

    Pair2(T sayi1, T sayi2){
        this.sayi1 = sayi1;
        this.sayi2 = sayi2;
    }

    public double carp(){
        return sayi1.doubleValue() * sayi2.doubleValue();
    }
}
class Pair5<T1 extends Number & Comparable<T1>, T2 extends Employee & Cloneable>{
    T1 sayi;
    T2 employee;
    
    Pair5(T1 sayi, T2 employee){
        this.sayi = sayi;
        this.employee = employee;
    }

    public void cloneEmployeeGetSayi() throws CloneNotSupportedException{
        T2 cloneEmployee = (T2)employee.clone();

        double maas = sayi.doubleValue();
    }
}

class Tricky1<T extends Comparable<? super T>> {
    T val1;
    T val2;

    public Tricky1(T val1, T val2) {
        this.val1 = val1;
        this.val2 = val2;
    }

    public T kiyasla() {
        // ÇALIŞIR! (Kusursuz Java Standartı)
        // Neden?: <? super T> kullandığımız için Java'ya şu garantiyi verdik:
        // "val1 ve val2 aynı tipte (T). val1'in compareTo metodu, T tipini YA DA T'nin 
        // atalarından (super) gelen bir tipi kabul edebilir."
        // Böylece val1'in içine val2'yi soktuğumuzda derleyici %100 Tip Güvenliği (Type Safety) sağlar.
        return (val1.compareTo(val2) > 0) ? val1 : val2; 
    }
}
class Tricky2<T> {
    T anaDeger;

    // Sınıfta tanımlı olmayan yeni bir 'K' tipini sadece bu metoda özel yaratıyoruz!
    // Metodun başına <K> koyarak derleyiciye "Bu yeni bir jenerik, haberin olsun" diyoruz.
    public <K> void baskaTiptenIslemYap(K baskaDeger) {
        System.out.println("Sınıfın tipi: " + anaDeger.getClass().getSimpleName());
        System.out.println("Metodun özel tipi: " + baskaDeger.getClass().getSimpleName());
    }
}
// mainde gidip Tricky2<Integer> t2 = new Tricky2<>(); t2.baskaTiptenIslemYap("selamınaleyükm"); babalar gibi çalışır fonksiyon zaten kendi içinde local genericini tanımlamış bozmaz T type'ın dan bişe yollamamamıza rağmen

// T1 ve T2'yi tanıtıyoruz. Kural: T1, T2 ile kıyaslanabilir olmak ZORUNDA! Benim ilk tipim, ikinci tipim ile 
class Tricky3<T1 extends Comparable<T2>, T2> {
    T1 kiyaslayan;
    T2 kiyaslanan;

    public Tricky3(T1 kiyaslayan, T2 kiyaslanan) {
        this.kiyaslayan = kiyaslayan;
        this.kiyaslanan = kiyaslanan;
    }

    public void hangisiBuyuk() {
        // T1, T2 ile compare edilebilir garantisi verdiğimiz için kod kusursuz çalışır!
        if (kiyaslayan.compareTo(kiyaslanan) > 0) {
            System.out.println("Birinci değer, ikinci değerden büyüktür.");
        }
    }
}
```

T extends ve T super farkı:

```java
import java.util.ArrayList;
import java.util.List;

// --- 1. HİYERARŞİMİZİ KURUYORUZ (Dede -> Baba -> Oğul) ---
class Calisan { 
    public void maasAl() { System.out.println("Çalışan maaşı yattı."); } 
}

class Yazilimci extends Calisan { 
    public void kodYaz() { System.out.println("Kod yazılıyor..."); } 
}

class Backendci extends Yazilimci { 
    public void dbBagla() { System.out.println("Veritabanı bağlandı."); } 
}

public class WildcardFantezisi {

    public static void main(String[] args) {
        List<Calisan> calisanListesi = new ArrayList<>();
        List<Yazilimci> yazilimciListesi = new ArrayList<>();
        List<Backendci> backendciListesi = new ArrayList<>();

        // Metotları test edelim:
        
        // okumaYap(calisanListesi); // COMPILE ERROR! Çalışan, Yazılımcı'nın üstüdür, extends kuralına uymaz.
        okumaYap(yazilimciListesi);  // BAŞARILI
        okumaYap(backendciListesi);  // BAŞARILI (Çünkü Backendci extends Yazilimci)

        eklemeYap(calisanListesi);   // BAŞARILI (Çünkü Calisan, Yazilimci'nin atasıdır/super'idir)
        eklemeYap(yazilimciListesi); // BAŞARILI
        // eklemeYap(backendciListesi); // COMPILE ERROR! Backendci, Yazilimci'nin atası değildir!
    }


    // =========================================================================
    // SENARYO 1: <? extends Yazilimci> -> ÜST SINIR (SADECE OKUMA - COVARIANCE)
    // KURAL: Liste sadece "Yazılımı" veya onun "Alt Sınıflarını" kabul eder.
    // =========================================================================
    public static void okumaYap(List<? extends Yazilimci> liste) {
        
        // ✅ OKUMA KUSURSUZ ÇALIŞIR:
        // Çünkü bu listede ne olursa olsun, en kötü ihtimalle bir "Yazılımcı" 
        // olduğunu %100 biliyoruz. (Backendci de gelse o da bir Yazılımcıdır).
        Yazilimci y = liste.get(0); 
        y.kodYaz(); 
        
        // 🚨 EKLEME YAPMAK ANINDA PATLAR! (COMPILE ERROR)
        // Neden? Çünkü dışarıdan buraya List<Backendci> gönderilmiş olabilir!
        // Sen buraya gidip yeni bir Yazilimci() veya baska bir alt sınıf eklersen
        // ve orijinal liste List<Backendci> ise, tipi bozmuş olursun.
        // Derleyici riske girmez ve "Kardeşim ben bu listenin asıl tipini bilmiyorum, eleman ekletmem" der.
        
        // liste.add(new Yazilimci()); // HATA!
        // liste.add(new Backendci()); // HATA!
    }


    // =========================================================================
    // SENARYO 2: <? super Yazilimci> -> ALT SINIR (SADECE EKLEME - CONTRAVARIANCE)
    // KURAL: Liste sadece "Yazılımcı" veya onun "Ata Sınıflarını (Calisan, Object)" kabul eder.
    // =========================================================================
    public static void eklemeYap(List<? super Yazilimci> liste) {
        
        // ✅ EKLEME KUSURSUZ ÇALIŞIR:
        // Çünkü bu liste "Yazilimci" veya onu kapsayan daha geniş bir liste (örn: Calisan).
        // Sen bu geniş listeye bir "Yazilimci" veya onun alt türü olan "Backendci"yi
        // paşalar gibi ekleyebilirsin. Elma sepetine elma atmak gibidir, sorun çıkmaz.
        liste.add(new Yazilimci());
        liste.add(new Backendci()); 
        
        // 🚨 OKUMA YAPMAK PATLAR! (COMPILE ERROR)
        // Neden? Çünkü dışarıdan buraya List<Calisan> veya List<Object> gönderilmiş olabilir!
        // Sen bu listeden bir eleman çekip onu "Yazilimci" değişkenine atamaya kalkarsan,
        // ya çektiğin eleman sadece sıradan bir "Calisan" ise? (Örn: İnsan Kaynakları personeli).
        // Derleyici "İçeriden ne çıkacağını garanti edemem" diyerek okumayı yasaklar!
        
        // Yazilimci y = liste.get(0); // HATA! (Gelen şey sadece Calisan olabilir)
        
        // Okuyabileceğin tek güvenli tip dünyadaki her şeyin atası olan Object'tir:
        Object guvenliOkuma = liste.get(0); // Bu çalışır ama pek bir işe yaramaz.
    }
}
```

Interface'ler içerisinde de generic yapılar kullanılabilir `interface myInterface<T>{}` şeklinde 

### Örnek 1
```java
public class Main{
    public static void main(String[] args) {
        Sample<Integer> s = new Sample<>();
        s.setData(10);
        System.out.println(s.getData());
        //s.setData("null");    //errora atar tanımlanan tip string olmadığı icin
        //s = new Sample<String>();  Sample<Double> s2 = s;   //convert veya cast da edilemez
        //s.setData(10.0);  //auto cast edilse bile yine olmaz ( aynısı double sample icin integer set etmeye çalışırken de olmaktadır)
        Sample<Double> s2 = new Sample<>(10);
        
    }
}
class Sample<T>{
    private T data;

    Sample(T data){
        this.data = data;
    }
    Sample(double doubleData){
        // this.data = doubleData;      // göründüğü üzere mümkün de değil zaten :D
    }
    Sample(int data){
        // şimdi gidip Sample<Integer> obj = new Sample<>(10); dersen hayat biter, burda zaten this.data = data; diyecek halimiz de yok :D, e hiç atanamadığı için de getData() null döner tabii ki.
    }
    Sample(){}

    public void setData(T data){this.data = data;}
    public T getData(){return data;}
}
```

### Örnek 2
```java
import java.util.ArrayList;

public class Main{
    public static void main(String[] args) {
        Test<Object> t = new Test<>();
        // ArrayList<Object> t  = new ArrayList<>();
        t.add((Integer)2);
        t.add((String)"hello");
        t.show();
    }
}
class Test<K>{
    ArrayList<K> list;
    //static K gizliDeger;  derlenmezzz

    Test(){
        list = new ArrayList<>();
    }

    void add(K data){
        list.add(data);
    }
    void show(){
        for(K data : list)
            System.out.print(data + " ");
    }
    void do_not(int gelenSayi){ // aynı zamanda bu method static de olamaz 
        // K obj = new K();     //SYNTAX ERROR
        K obj2;
        //K[] array = new K[1];     //SYNTAX ERROR
        K[] array2;
        //if(gelenSayi instanceof K)    // type check yapılamaz

        ArrayList<K> list = new ArrayList<>();
        ArrayList<K> list2;
        //try{}catch(T e){} // generic bir şey catch edilemez mantıken :D
    }
}
```

### Örnek 3
```java
public class Main<F,S>{
    
    public static void main(String[] args) {
        Pair<Integer, String> p1 = new Pair(1, "testing");
        Pair<Double, Integer> p2 = new Pair(10.5);
        System.out.println(p1);
        System.out.println(p2);

        // bu kısım da hata atacaktır, önceki örnekteki do_not'lar gibi
        //String data = "data";
        //F dataF = (F)data;
    }
}
class Pair<F,S>{
    F first;
    S second;

    Pair(F first, S second){
        this.first = first;
        this.second = second;
    }
    Pair(S second){
        this.second = second;
        // this.first = (int)this.second;   //bilinemedği için convert edilemez
        this.first = (F)"belirtilmemiş F"; // cast etmezsen sıçar, bu hali de warning atar ama yine de çalışır
    }
    // Main(F first){}      // bu constructor oluşturulamaz, çünkü bilinmeyen iki tip de aynı şeye refer edecek olabilir main içinde kullanan bunu bilemez. Ya yukarıdaki ya da bu kullanılacak
    public String toString(){
        return "F: " + first + ", S: " + second;
    }
}
```

### Örnek 4
```java
interface myInterface<T>{
    T getValue();
    void setValue(T data);
}
class GenericContainer<T,K> implements myInterface<T>{    // generik isimlendirmeler uyuşmalı, başka hiçbir şekilde kabul olmaz yoksa. K sınıfın kendiliğinden eklenmiş olabilir, class GenericContainer<A, K> implements myInterface<A> da olabilirdi tabii ki. ilki diğerine uysun yeter.
    T value;
    K value2;
    public T getValue(){return this.value;}
    public K getKValue(){return this.value2;}
    public void setValue(T data){
        value = data; 
        value2 = (K)((Integer)(3));
    }
    public <GECICI_OLARAK,KULLANILABILIR> void printValue(GECICI_OLARAK value3, KULLANILABILIR value4){   // belirtilen <t3,t4> kısmı olmasaydı bu değişkenleri oylesine kullanamazdık.
        System.out.println("types of t's: \n\tvalue3: " + value3.getClass().getName() + "\n\tvalue4: " + value4.getClass().getName());
        System.out.println("value3: " + value3);
        System.out.println("value4: " + value4);
        System.out.println("casted value: " + (T3) value4);
    }
}
class StringContainer<K> implements myInterface<String>{    // Biz zaten myInterface<String> diyerek onu doyurmuşuz gerisinin önemi yok, ha istersen T yaz tabi bozmaz
    public String getValue() { throw new UnsupportedOperationException("Unimplemented method 'getValue'");}
    public void setValue(String data) { throw new UnsupportedOperationException("Unimplemented method 'setValue'");}
}
public class Main{
    public static void main(String[] args) {
        GenericContainer<String, Integer> genericContainer = new GenericContainer<>();
        genericContainer.setValue("testdata");
        System.out.println(genericContainer.value2.getClass().getName());   // integer olarak return eder ancak biz setValue içerisinde value2 = (K)"test" deseydik bu satırda hata atılacaktı convert edemediği için, ilginçtir ki string olsa bile aşağıda getKValue() dediğimizde string value'yuı dönebiliyor <Integer> olarak tanımlamamıza rağmen,
        System.out.println(genericContainer.getValue());
        System.out.println(genericContainer.getKValue());
        // ==========================================================================================================================================
        GenericContainer<Integer, String> container = new GenericContainer<>();
        container.setValue(10);
        System.out.println("value: " + container.getValue());
        container.printValue(false, "falseString");
        container.printValue("10.5 string", 10.5);
        container.<Integer,Boolean>printValue(0,false);
        //container.<String, Integer>printValue(0,0);       //syntax error belirtilen type yollanmadı
        // ==========================================================================================================================================
        // aşağıdaki fonksiyonu kullanma yöntemleri
        int result = getMiddleIndex(new Integer[] {1, 2, 3});
        result = Main.<String>getMiddleIndex(new String[]{"ali", "ahmet", "mehmet"});
        result = Main.getMiddleIndex(new Boolean[]{false, true, false});
    }

    // yukarıdaki örnek buradaki gibi de kullanılabilir illa sınıfın generic olmasına gerek yok
    public static <ARRAY_TURU> int getMiddleIndex(ARRAY_TURU arr[]){
        if(arr!=null && arr.length > 0)
            return arr.length/2;
        return -1;
    }
}
```

### Örnek 5
```java
public class Main{
    public static void main(String[] args) {
        GenericStatic.printData(10);
        GenericStatic.<Boolean>printData(false);
        // GenericStatic.<String>printData(10); // bi zahmet olasmın 
    }
}
class GenericStatic<T>{
    // static T staticT; // Removed because static fields cannot use generic type parameters
    T nonStaticT;

    // aşağıdaki iki fonskyion hata atar çünkü static metotlar içerisinde generic veri yapıları kullanılamaz
    /* static void setData(T data){
        System.out.println("new data: " + data);
    } 
    static void printData(){
        System.out.println("data: " + nonStaticT);
    } */
    
    // bu olur ama, sonuçta dışarıdan alınacak bir fonksyion bu
    static<TEST> void printData(TEST data){
        System.out.println("data: " + data);
    } 
}
```

### Örnek 6
```java
class Pair<T>{
    T first;
    T second;

    Pair(T first, T second){
        this.first = first;
        this.second = second;
    }
}
class StringPair extends Pair<String>{
    StringPair(String first, String second){
        super(first, second);
    }
    // bla bla
}
class GenericPair<T> extends Pair<T>{
    GenericPair(T first, T second){
        super(first, second);
    }
    // bla bla
}
class E<T,U> extends Pair<String>{
    T selfT;
    U selfU;

    E(String first, String second) {
        super(first, second);
    }

}
/* 
KISA İZİN VERİLEN VE VERİLMEYENLER ÖZET:
class GenericSubClass1<T> extends GenericSuperClass<T>
class GenericSubClass2<T, V> extends GenericSuperClass<T>
class GenericSubClass3<T1, T2> extends GenericSuperClass<T>     //error, T'nin de kullanılması lazım subclass'ta.
class C<T,U> extends Pair<T>{}
class D<U,T> extends Pair<T>{}
class E<T,U> extends Pair<String>{}     // örneği yukarıda. pair type belirterek subda kullanılan T, base classs'taki T'den farksız.
class F extends Pair<String>{}
class G extends Pair<T>{}       //error T'yi bizim de kullanmamız lazım subclass'ta
*/
```

### Örnek 7
```java
import java.util.Arrays;
import java.util.List;

public class Main{
    public static void main(String[] args) {


        List<String> stringList = Arrays.asList("kerem", "test", "test2");
        logData(stringList);
        logData2(stringList);
    }
    // logData2'nin aksine ? ile wildcard koyup geçebiliyoz, UYARI: her ikisinde de tip belli olmadığından listeye yeni elemen eklenemez.
    public static void logData(List<?> list){
        for(Object element : list)
            System.out.println(element.toString());
    }
    public static<T> void logData2(List<T> list){
        for(Object element : list)
            System.out.println(element.toString());
    }
}
class Example{
    // ? field1;    // bu olmaz java der bu ne la ben ram'de ne kadar alan açacağımı bilmiyom diye
    List<?> field2; // bu olur çünkü içi ne olacaksa olsun bu bi list, tut gitsin.

    //ne olduğu belirli olmayan bi liste alırım da dönerim de 
    private List<?> method(){
        return null;
    }
    private List<?> method2(List<?> param){
        return param;
    }
    // ama en güzeli:
    private <T> T method3(List<T> param /* veya */ T param){
        return param;
    }

    // ? dönülür okey ama döneceğim adam ne? bu sorunun cevabı da verilemeyeceğinden olmaz
    /* private ? method1(int param){
        return param;
    }
    
    // bu olmaz çünkü param'ın ne olduğunu bilmediğinden neyini kullanacağını da bilemez.
    private int method1(? param){
        return 0;
    } */
}
```

Genericlerde wildcard bound'ları altında üçe ayrılır.

- Unbounded wildcards
- Upper-bounded wildcards
- Lower-bounded wildcards, örnekleri sırası ile aşağıda

### Örnek 8 - Unbounded Wildcards
```java
import java.util.List;
import java.util.Arrays;

public class Main {
    
    // Metodumuz her türlü listeyi kabul ediyor
    public static void listeyiYazdir(List<?> liste) {
        
        // GÜVENLİ OKUMA: İçeride ne olduğunu bilmediğimiz için 
        // Java hepsini en tepe sınıf olan "Object" olarak okumamıza izin verir.
        for (Object eleman : liste) {
            System.out.print(eleman + " ");
        }
        System.out.println();
        
        // 🚨 ÖLÜMCÜL TUZAK (YAZMA YASAĞI) 🚨
        // liste.add(10); // HATA! (Compile Error)
        // liste.add("Elma"); // HATA! (Compile Error)
        // liste.add(new Object()); // HATA! (Compile Error)

        // NEDEN YASAK?
        // List<?> ile tanımlanan bir listeye ne tipte eleman eklenebileceğini Java bilemez.
        // Çünkü ? (wildcard) herhangi bir tip olabilir.
        // Bu yüzden derleyici, güvenlik için hiçbir eklemeye izin vermez.
        // Sadece okuma (read) işlemi güvenlidir, yazma (add) işlemi yasaktır.
    }

    public static void main(String[] args) {
        List<Integer> intListesi = Arrays.asList(1, 2, 3);
        List<String> stringListesi = Arrays.asList("Java", "C++", "Python");
        List<?> randomListe = Arrays.asList("kerem", 23);
        
        // Sınırsız joker ikisini de paşalar gibi kabul eder!
        listeyiYazdir(intListesi);
        listeyiYazdir(stringListesi);
        listeyiYazdir(randomListe);
    }
}
```
### Örnek 9 - Upper-bounded Wildcards
```java
import java.util.List;
import java.util.Arrays;

public class Main {
    // Slayttaki örneğin aynısı: Listeye Number veya onun çocukları (Integer, Double vb.) gelebilir.
    public static double sumOfList(List<? extends Number> list) {
        double sum = 0.0;
        for (Number n : list) {
            // GÜVENLİ OKUMA: İçindekilerin kesinlikle Number soyundan geldiğini biliyoruz.
            sum += n.doubleValue();
        }
        
        // BÜYÜK TUZAK: list.add(10); // HATA VERİR!
        // Neden? Çünkü gelen liste List<Double> da olabilir. Double listesine Integer (10) ekleyemezsin. 
        // O yüzden "? extends" SADECE OKUMAK İÇİNDİR!
        return sum;
    }

    public static void main(String[] args) {
        List<Integer> intListesi = Arrays.asList(1, 2, 3);
        List<Double> doubleListesi = Arrays.asList(1.5, 2.5);
        
        // İkisi de Number'ın çocuğu olduğu için derleyici itiraz etmez!
        System.out.println("Toplam (Int): " + sumOfList(intListesi));
        System.out.println("Toplam (Double): " + sumOfList(doubleListesi));
    }
}
```
### Örnek 10 - Lower-bounded Wildcards
```java
import java.util.List;
import java.util.ArrayList;

public class Main {
    // Slayttaki örnek: Parametre olarak sadece Integer, Number veya Object listesi alabilir.
    public static void addNumbers(List<? super Integer> list) {
        // GÜVENLİ YAZMA: İçeriye korkusuzca Integer ekleyebiliriz.
        for (int i = 1; i <= 5; i++) {
            list.add(i); 
        }
        
        // BÜYÜK TUZAK: Number n = list.get(0); // HATA VERİR! (Özel Type Cast işlemi gerekir)
        // Neden? Çünkü gönderilen liste List<Object> olabilir. Object'i doğrudan Number değişkenine atayamazsın.
        // O yüzden "? super" SADECE YAZMAK/EKLEMEK İÇİNDİR!
    }

    public static void main(String[] args) {
        List<Integer> intListesi = new ArrayList<>();
        List<Number> numListesi = new ArrayList<>();
        List<Object> objListesi = new ArrayList<>();
        List<String> stringList = new ArrayList<>();
        
        // Üç listeye de rahatça eleman ekletebiliriz!
        addNumbers(intListesi);
        addNumbers(numListesi);
        addNumbers(objListesi);
        //addNumbers(stringList);       // string integer super etmez :D
    }
}
```

### Karışık Örnekler

```java
public void listeyiGuncelle(List<? extends Number> liste) {
    liste.add(10.5); // Double eklemeye çalışıyoruz
}

//output: hata verir,? extends Number bu liste number veya alt sınıfları tutmaktadır der ancak hangisi olduğunu bilemez. dışarıdan list<integer> yollanmış ise patlar mesela dolayısı ile riski almak istemez. ayrıca extends olarak verilmiş ise yalnızca ya okuma yapılabilir ya da null yazılabilir başka eleman eklenemez.
```

```java
// A Metodu
public void ekleA(List<?> liste) {
    liste.add(new Object()); 
}

// B Metodu
public <T> void ekleB(List<T> liste, T eleman) {
    liste.add(eleman); 
}
//output : B derlenir ancak A derlenmez çünkü A'nın ne olduğu bilinmediği için Object bile olsa ekleme işlemi yapılamaz
```

```java
//VeriOkuyucu adında static bir metot yaz. Bu metot parametre olarak bir List almalı. Bu liste; Yazilimci sınıfını veya Yazilimci'dan türeyen herhangi bir alt sınıfı (Backendci gibi) kabul etmeli. (Not: Sadece okuma yapılacağı için en dar kısıtı kullan).
public static void VeriOkuyucu(List<? extends Yazilimci> list){}

//Becerekli adında bir Generic Sınıf tanımla. Bu sınıfın alacağı T tipi; hem Employee sınıfından türemiş olmalı, hem de Cloneable arayüzünü (interface) implement etmiş olmalı.
public class Becerekli<T extends Employee & Cloneable>{}

//Sıradan (Generic olmayan) bir sınıfın içine static bir metot yaz. Metodun adı tipiDonustur olsun. Bu metot dışarıdan herhangi bir T tipinde nesne alsın ve geriye yine aynı T tipinde bir nesne dönsün. (Sınıf generic değil ama metot kendi tipini tanımlamalı!)
public static <T> T tipiDonustur(T nesne){}

//GeriyeDonukEkle adında static bir metot yaz. Bu metot bir List ve bir de Integer tipinde nesne almalı. Liste öyle bir yapıda olmalı ki; içine Integer eklenebilsin (Yani Integer'ın kendisini veya tüm atalarını kabul edebilmeli).
public static void GeriyeDonukEkle(List<? super Integer>, Integer n){}  // ekleme yaparken hani ? kullanamıyorduk. Cevabı:
/*
1. List<?> (Sınırsız Joker) 🚫 EKLEME YASAK
Senin dediğin durum bu. Java der ki: "Kardeşim bu listenin içinde her şey olabilir (String, Integer, Elma, Armut). Ben bunun ne olduğunu %100 bilmediğim için içine Object dahi ekletmem, çünkü gidip List<String> içine Integer atıp tipi bozabilirsin." * Özet: Sadece okumak (read) içindir.

2. List<? super Integer> (Alt Sınır Joker) ✅ EKLEME SERBEST
İşte sihir burada! Java der ki: "Bu liste ya Integer listesidir, ya Number listesidir ya da Object listesidir. Bu üç ihtimalin de ortak özelliği nedir? Hepsi Integer'ı içinde barındırabilir!"

Sen bu listeye 10 (Integer) eklemek istediğinde; liste List<Number> bile olsa Integer bir Number olduğu için hata çıkmaz.

Kural: Eğer bir listeye ekleme yapacaksan (Consumer), her zaman super kullanmalısın.
*/
/*

GENEL ?, SUPER VE EXTENDS TABLOSU

List<? extends T> durumunda okuma güvenli her türlü T type'tan döner maksimum. Yazma ise yasaktır. Daraltılmış liste anlamına gelir içindeki her şey en azından bi T tipindedir.
List<? super T> okumak riskli, Object dönmektedir. Yazmak garanti her türlü T yazılacaktır. Bu da genişletilimiş liste anlamına gelir. T'yi içine alabilecek kadar büyüktür dolayısı ile rahatlıkla T eklenebilir ama içinde ne olduğu belli olmaz tabii.
List<?>'te ise okuma güvenlidir ancak her türlü Object döner. Yazmak da yine yasaktır Object bile olsa. Bilinmezlik anlamına geldiği için yalnızca okuma yapılabilmektedir.
*/
```

```java
class Depo<T> {
    T[] veriler;
    Depo(T[] gelen) { this.veriler = gelen; }
    
    public T[] kopyala() { return veriler.clone(); }
}

// Main içinde:
Integer[] dizi = {1, 2, 3};
Depo<Integer> depo = new Depo<>(dizi);
Integer[] kopya = depo.kopyala();
kopya[0] = 99;

System.out.println(dizi[0]);
// output: 1, shallow copy olmaz .clone() ile yapıldığı için farklı referanstadır. ancak [][] olsaydı shallow olurdu o da ilginç.
```

```java
class Kutu<T> {
    T veri;

    Kutu(T veri) { this.veri = veri; }

    // Sınıfın T'sinden bağımsız, yeni bir T tanımlıyoruz! (Shadowing)
    public <T> void print(T yeniVeri) {
        System.out.println("Sınıf: " + this.veri + " | Metot: " + yeniVeri);
    }
}

public class Main {
    public static void main(String[] args) {
        Kutu<Integer> kutu = new Kutu<>(100);
        kutu.<String>print("Merhaba");
        kutu.print(true);
        //kutu.<Double>print(10); bu tarz basit castlar bile yapılamamakta
    }
}
// output: 100 merhaba, Sınıf: 100 | Metot: true
```

```java
Test<Integer> obj6 = new Test<Integer>(3);
Test<String> obj7 = new Test<String>("kerem");
Test<String> obj8 = new Test<String>("elma");
System.out.println("obj6: " + obj6);        //prints the object with its address
System.out.println("obj6.getObj(): " + obj6.getObj());      //return the actual integer value cuz getFunction retrun Obj and we idetnfied Obj as integer while creating the object
System.out.println("obj7: " + obj7);
System.out.println("obj7.getObj(): " + obj7.getObj());
System.out.println("obj8: " + obj8);
System.out.println("obj8.getObj(): " + obj8.getObj());
//obj6 = obj7;  //gives error "java.lang.String cannot be converted to java.lang.Integer"
obj8 = obj7; // veya obj7 = obj8 does not matter, shallow copy we did, same addresses
```

```java
GenericClassExample<?,?,?>[] array = new GenericClassExample<?,?,?>[3];

/*
hata atmaz.

veri yazarken herhangibir GenericClassExample varyasyonu eklenebilir <String, Integer, Double>, <Integer, Integer, Integer> tarzı hiç fark etmeden eklenebilir
veri okuma kısmında ise object olarak okunur düzenli cast edilmesi gereklidir. Object o = array[0].getT();

*/


GenericClassExample<String,?,?> m3 = new GenericClassExample<>("kerem","kerem",3); 

/*
hata atmaz.

birinci parametrede tip belirtilmiş ancak diğer tipler ? bırakılmıştır. Ancak compile edilikrne tip tahmini yapılmıştır <String, String, Integer> olarak. 

önemli nokta şu: m3.getT() direkt olarak String dönerken getU() ve getV() bunlar sadece Object döner çünkü asıl belirlendikleri tipler ? olduğundandır. 
Önemli nokta bi de: m3.setU() ve m3.setV() kısmında özel bir extends durumu yoksa Java izin vermez. çünkü m3'ün ne beklediği bilinmemektedir. extends durumunun örneği aşağıda:

// "U tipi en azından bir Number olsun, gerisi mühim değil"
GenericClassExample<String, ? extends Number, ?> m4 = new GenericClassExample<>("kerem", 10.5, true);

// OKUMA (GET):
Number n = m4.getU(); // TERTEMİZ! Java bilir ki oradan gelecek şey en kötü ihtimalle bir Number'dır.
Double d = (Double) m4.getU(); // Yine cast lazım ama en azından Number olduğunu biliyoruz.
*/

```

## Collections

- ArrayList
- LinkedList
- Tree
- HashMap
- Stack
- Queue
- HashSet
- TreeSet
- TreeMap
- Iterator
- LinkedHashSet

Java Collections Framework(JCF) içerisinde interface'ler, abstract sınıflar vs bulunur. İçerisinde list, stack, queue, set, map gibi yapılar tutar. `Collection<T>` interface'i bu yapıların basic operasyonlarını içerir. Metotları:

HashSet'te Fill ratio/load factor 0.75 olduğu zaman yeni HashSet objesi oluşturulur otomatik olarak.

```java
boolean isEmpty()
boolean contains(Object target)
int hashCode()
boolean containsAll(Collection<?> collectionOfTargets)
boolean equals(Object other)
int size()
Iterator<T> iterator()
Object[] toArray()
<E> E[] toArray(E[] a)
String toString()
// opsiyonel olanlar aşağıda
boolean add(T element)  // mesela Set'te duplike olmaması dikkat edilmemeli, kendi collections kütüphanesinde buna özel gelmekte zaten.
boolean addAll(Collection<? extends T> colleactionToAdd)
boolean remove(Object element)
boolean removeAll(Collection<? extends T> collectionToRemove)
void clear()
boolean retainAll(Collection<?> saveElements)
// bazı diğer metotlar da bazı veri yapılarında hazır gelmekte, bazıaları implement edilebilir. mesela sort için:
public static <T extends Comparable<? super T>> void sort(List<T> list)
// mesela list için özel olanlar
void add(int index, T element)
boolean addAll(int index, COllection<? extends T> collectionsToAdd)
T get(int index)
T set(int index, T newElement)
T remove(int index)
List<T> subList(int fromIndex, int toIndex)
int indexOf(Object target)
int lastIndexOf(Object target)
// linkedlist özel
addLast
addFirst
getFirst
getLast
removeFirst
listIterator    // previous özelliği de varr
// stack özel
push()
pop()
peek()
// queue özel -> aynı zmaanda priolu hali için PriorityQueue<String> q = new PriorityQueue<>();
add()
remove()
peek()
// map özel
put()
get()
containsKey()
containsValue()
putAll()
keySet()
values()
entrySet()
// iterator özel -> Iterator<String> iter = names.iterator() şeklinde instance oluşturulur
hasNext()
hasPrevious()
next()
previous()
remove()
add() -> list iterator özel
```


### Linked List
```java
class LinkedList<T>{
    private Node head;
    private int size;
    class Node{
        private T data;
        private Node next;

        Node(T data){
            this.data = data;
        }

        public T getData(){
            return this.data;
        }
    }
    LinkedList(){
        head = null;
    }

    // add functions
    public void add(T data, int index){
        if(index == 0)
            addToStart(data);
        else if(index >= size){
            addToLast(data);
        }
        else{
            Node curr = head;
            for(int i=0; i<index-1; i++){
                curr = curr.next;
            }
            Node newNode = new Node(data);
            newNode.next = curr.next;
            curr.next = newNode;
            size++;
        }
    }
    private void addToStart(T data){
        if(head == null){
            head = new Node(data);
        }
        else{
            Node oldHead = head;
            Node newNode = new Node(data);
            head = newNode;
            head.next = oldHead;
        }
        size++;
    }
    private void addToLast(T data){
        Node curr = head;
        while(curr.next != null)
            curr = curr.next;
        curr.next = new Node(data);
        size++;
    }

    // remove functions
    public boolean remove(int index){
        if(size == 0){
            System.out.println("liste bos");
            return false;
        }
        if(index >= size){
            System.out.println("orda index yok la");
            return false;
        }
        else if(index == 0)
            return deleteHeadNode();
        else if(index == size-1)
            return deleteTailNode();
        else {
            Node curr = head;
            for(int i = 0; i < index - 1; i++) {
                curr = curr.next;
            }
            curr.next = curr.next.next;
            size--;
            return true;
        }
    }
    private boolean deleteHeadNode(){
        if(head == null)
            return false;
        else if(head.next == null)
            head = null;
        else
            head = head.next;
        
        size--;
        return true;
    }
    private boolean deleteTailNode(){
        if(size == 1){
            return deleteHeadNode();
        }
        Node curr = head;
        while(curr.next.next != null){
            curr = curr.next;
        }
        curr.next = null;
        size--;
        return true;
    }

    // size functions
    public int getSize(){
        return this.size;
    }
    public int sizeRecursive(Node curr){
        if(head == null)
            return 0;
        return sizeRecursive(curr.next) + 1;
    }
    public int sizeIterative(){
        int count = 0;
        Node curr = head;
        while(curr != null){
            count++;
            curr = curr.next;
        }
        return count;
    }

    // print functions
    public String toString(){
        StringBuilder s = new StringBuilder();
        if(size == 0)
            return "List is empty";
        Node curr = head;
        while(curr != null){
            s.append(String.valueOf(curr.data) + ", ");
            curr = curr.next;
        }
        return s.substring(0, s.lastIndexOf(","));
    }

    // other
    public Node find(T target){
        Node curr = head;
        while(curr != null){
            if(curr.data == target)
                return curr;
            curr = curr.next;
        }
        System.out.println("target bulunamadı");
        return null;
    }
    public boolean isEmpty(){
        return head == null;
    }
    public void clear(){
        head = null;
    }
}


public class Main{
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>();
        list.add("hello", 0);
        list.add("world", 0);
        list.add("java", 0);
        list.add("c", 0);
        System.out.println(list);
    }
}
```


### Stack_Example
```java
import java.util.Stack;

// Class representing a simple web browser
class WebBrowser {
    private Stack<String> history;

    public WebBrowser() {
        history = new Stack<>();
    }

    // Method to visit a new page
    public void visitPage(String page) {
        System.out.println("Visiting page: " + page);
        history.push(page); // Add the page to history
    }

    // Method to navigate back to the previous page
    public void goBack() {
        if (!history.isEmpty()) {
            String previousPage = history.pop(); // Retrieve the previous page
            System.out.println("Navigating back to page: " + previousPage);
        } else {
            System.out.println("No previous page available");
        }
    }
}

public class Stack_Example {
    public static void main(String[] args) {
        // Create a WebBrowser instance
        WebBrowser browser = new WebBrowser();

        // Visit some pages
        browser.visitPage("https://www.example.com");
        browser.visitPage("https://www.google.com");
        browser.visitPage("https://www.wikipedia.org");

        // Navigate back
        browser.goBack(); // Navigating back to page: https://www.google.com
        browser.goBack(); // Navigating back to page: https://www.example.com
        browser.goBack(); // Navigating back to page: null
        browser.goBack(); // No previous page available
    }
}
```

### UniqueArray ve Interface Metotları
```java
import java.util.*;

class UniqueArrayList<E> extends ArrayList<E> {

    @Override
    public boolean add(E e) {
        if (!contains(e)) {
            return super.add(e);
        }
        return false;
    }

    // main method
    public static void main(String[] args) {
        UniqueArrayList<Integer> uniqueList = new UniqueArrayList<>();
        uniqueList.add(1);
        uniqueList.add(2);
        uniqueList.add(3);
        uniqueList.add(1);
        System.out.println(uniqueList); // [1, 2, 3]
    }
}

interface Collection<E> {
    // Basic Operations
    boolean add(E e);

    boolean remove(Object o);

    boolean contains(Object o);

    boolean isEmpty();

    int size();

    void clear();

    // Bulk Operations
    boolean addAll(Collection<? extends E> c);

    boolean removeAll(Collection<?> c);

    boolean retainAll(Collection<?> c);

    boolean containsAll(Collection<?> c);

    // Array Operations
    Object[] toArray();

    <T> T[] toArray(T[] a);

    // Iterator Operations
    Iterator<E> iterator();

    // Comparison and hashing
    boolean equals(Object o);

    int hashCode();

    // Optional Methods
    default Stream<E> stream() {
        // Implementation specific
    }

    default Stream<E> parallelStream() {
        // Implementation specific
    }

    // Spliterator
    default Spliterator<E> spliterator() {
        // Implementation specific
    }
}

interface ArrayList2<E> {
    // ArrayList specific methods
    void trimToSize();

    void ensureCapacity(int minCapacity);

    void add(int index, E element);

    boolean addAll(int index, Collection<? extends E> c);

    E remove(int index);

    E get(int index);

    E set(int index, E element);

    int indexOf(Object o);

    int lastIndexOf(Object o);

    ListIterator<E> listIterator();

    ListIterator<E> listIterator(int index);

    List<E> subList(int fromIndex, int toIndex);
}

interface Vector2<E> {
    // Vector specific methods
    void addElement(E obj);

    boolean removeElement(Object obj);

    void removeElementAt(int index);

    void removeAllElements();

    void insertElementAt(E obj, int index);

    void setElementAt(E obj, int index);

    E elementAt(int index);

    Enumeration<E> elements();

    int indexOf(Object obj);

    int lastIndexOf(Object obj);

    void setSize(int newSize);

    void trimToSize();

    void ensureCapacity(int minCapacity);
}

interface LinkedList2<E> {
    // List Interface Methods:
    void add(int index, E element);

    boolean addAll(int index, Collection<? extends E> c);

    E remove(int index);

    E get(int index);

    E set(int index, E element);

    int indexOf(Object o);

    int lastIndexOf(Object o);

    ListIterator<E> listIterator();

    ListIterator<E> listIterator(int index);

    List<E> subList(int fromIndex, int toIndex);

    // Queue Interface Methods
    // (LinkedList implements Queue):
    boolean offer(E e);

    E remove();

    E poll();

    E element();

    E peek();

    void push(E e);

    E pop();
}
```

### HashMap, Set, HashSet, LinkedHashSet, TreeSet

HashSet'te sıralama mantığı yok, hızlı; LinkedHashSet'te eklenme sırasına göredir, orta hızdadır; TreeSet'te doğal sıralama vardır, yavaştır O(logn). Ne zaman ne kullanılmalı?

1. HashSet: "Hız Her Şeydir, Sıra Hiçbir Şey". Eğer elemanların hangi sırayla durduğu zerre kadar umurunda değilse, sadece "Var mı yok mu?" kontrolünü ışık hızıyla yapmak istiyorsan tek tercihin bu olmalı.Ne zaman kullanmalı? Bir listede mükerrer (duplicate) kayıtları temizlemek için veya bir ID listesinde hızlıca arama yapmak için.Mekanizma: Arka planda HashMap kullanır. Elemanları "Hash" değerlerine göre rastgele kutulara atar.Performans: Ekleme, silme ve arama işlemleri sabit sürede (O(1)) biter. En hızlısı budur.

2. LinkedHashSet: "Girdiğim Gibi Çıksın". HashSet'in hızını seviyorsun ama elemanların içeri giriş sırasını da korumak istiyorsan bunu kullanmalısın. Ne zaman kullanmalı? Bir "Son Arananlar" listesi veya bir formda seçilen hobiler listesi yapıyorsan. Kullanıcı hangi sırayla tıkladıysa, ekranda da o sırayla (ama benzersiz şekilde) görmek ister.Mekanizma: HashSet'e ek olarak bir de Doubly-Linked List (Çift Yönlü Bağlı Liste) tutar. Bu liste elemanların giriş sırasını hafızaya kazır.Performans: HashSet'ten bir tık (ihmal edilebilir düzeyde) yavaştır çünkü her eklemede o bağlı listeyi de güncellemesi gerekir.

3. TreeSet: "Her Zaman İp Gibi Dizili Olsun". Elemanların her daim sıralı (küçükten büyüğe veya alfabetik) durmasını istiyorsan, içeri ne atarsan at o kendi içinde hizaya sokar.Ne zaman kullanmalı? Bir skor tablosu, alfabetik bir rehber veya bir sayı aralığındaki elemanları çekmek (örn: 10 ile 50 arasındaki sayılar) istiyorsan.Mekanizma: Arka planda Red-Black Tree (Dengeli İkili Arama Ağacı) kullanır. Performans: En yavaşı budur ($O(\log n)$). Çünkü her yeni eleman geldiğinde ağacı baştan aşağı gezip doğru yeri bulur ve ağacı yeniden dengeler.🚨 Kritik Not: İçine koyduğun objelerin Comparable olması (kıyaslanabilir olması) şarttır! Yoksa "Ben bunları neye göre dizeceğim?" diyerek hata verir.

```java
Set<String> s = new HashSet<>();
s.add("Ali");      // Eleman ekler (Varsa eklemez, false döner)
s.remove("Ali");   // Eleman siler
s.contains("Ali"); // Var mı yok mu? (HashSet'te ışık hızıyla bakar)
s.size();          // Eleman sayısı
s.clear();         // Her şeyi siler
```

### Set ve Map'ler

```java

class HashSetExample {

    public static void main(String[] args) {
        // Create a HashSet
        HashSet hset = new HashSet<String>();   //şimdi HashSet<String> diyerek en başta tanımlamadığımız için Java çok umursamaz, sonda new'den sonra ne yazdığına falan çok bakmaz. O yüzden integer da kabul edilmekte. 
        // Adding elements to the HashSet
        hset.add("Apple");
        hset.add(1);

        Set linkedHashSet = new LinkedHashSet<String>();
        // Add heterogeneous elements to the LinkedHashSet
        linkedHashSet.add("apple");
        linkedHashSet.add(123);
        linkedHashSet.add(3.14);
        linkedHashSet.add(true);

        Set treeSet = new TreeSet<>();
        // Add heterogeneous elements to the TreeSet
        treeSet.add(12.3);
        treeSet.add(3.14);
        //treeSet.add("apple");   // bu satırda hata atar, treeset kendi içerisinde compare edip sıraya dizmekte, şimdiye kadar integer okumuş ancak burada string görünce patlar. hata atar.

        // Displaying the HashSet
        System.out.println(hset); // [Apple, 1]
        System.out.println(linkedHashSet); // [apple, 123, 3.14, true]
        System.out.println(treeSet); // [12.3, 3.14]

    }

}

class SetExample {
    public void whenIteratingTreeSet_shouldIterateTreeSetInAscendingOrder() {
        Set<String> treeSet = new TreeSet<>(); 
        treeSet.add("First");
        treeSet.add("Second");
        treeSet.add("Third");
        treeSet.add("Fourth");
        Iterator<String> itr = treeSet.iterator();  //Iterator itr olarak tanımlansaydı eğer yine çalışırdı ancak warning atardı. next() çağırıldığında ise Object dönerdi. <?> konulsaydı da yine aynı muamele olurdu.
        // eğer sol tarafta <> işareti kullanacaksan içini dolduracaksın kesin kuraldır.
        while (itr.hasNext()) {
            System.out.println(itr.next());
        }
    }

    public static void main(String[] args) {
        SetExample setExample = new SetExample();
        setExample.whenIteratingTreeSet_shouldIterateTreeSetInAscendingOrder();
    }
}



interface Map2<K, V> {

    // Basic Operations
    // Add one key value pair to the Map.
    // If the key is present, then old value will be replaced 
    // with new value and returns old value
    V put(K key, V value);
    V get(Object key);
    boolean containsKey(Object key);
    boolean containsValue(Object value);
    V remove(Object key);
    int size();
    boolean isEmpty();
    void clear();

    // Bulk Operations
    void putAll(Map<? extends K, ? extends V> m);

    // Collection Views
    Set<K> keySet();
    Collection<V> values();
    Set<Map.Entry<K, V>> entrySet();

    // Nested Interface Entry
    interface Entry<K, V> {
        K getKey();
        V getValue();
        V setValue(V value);
        boolean equals(Object o);
        int hashCode();
    }

    // Comparison and hashing
    boolean equals(Object o);
    int hashCode();
}
```

### Set ve HashSet
```java
import java.util.HashSet;
import java.util.Set;
import java.util.TreeSet;
import java.util.Comparator;
import java.util.Arrays;

// Class representing a student
class Student {
    private int id;
    private String name;
    private double gpa;

    public Student(int id, String name, double gpa) {
        this.id = id;
        this.name = name;
        this.gpa = gpa;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public double getGpa() {
        return gpa;
    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", gpa=" + gpa +
                '}';
    }
}
// Comparator for sorting students by ID
class CompareById implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return Integer.compare(s1.getId(), s2.getId());
    }
}
// Comparator for sorting students by name
class CompareByName implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return s1.getName().compareTo(s2.getName());
    }
}

// Comparator for sorting students by GPA
class CompareByGpa implements Comparator<Student> {
    @Override
    public int compare(Student s1, Student s2) {
        return Double.compare(s1.getGpa(), s2.getGpa());
    }
}

class Example_Basic {
    public static void main(String[] args) {
        // Create a HashSet to store unique students (order not guaranteed)
        Set<Student> studentHashSet = new HashSet<>();
        studentHashSet.add(new Student(101, "Alice", 3.7));
        studentHashSet.add(new Student(102, "Bob", 3.9));
        studentHashSet.add(new Student(103, "Charlie", 3.5));
        studentHashSet.add(new Student(101, "David", 3.8)); 

        // Print the students in the HashSet
        System.out.println("HashSet - Unique Students (Order not guaranteed):");
        for (Student student : studentHashSet) {
            System.out.println(student);
        }

        System.out.println();

        // Create a TreeSet to store unique students based on their IDs
        TreeSet<Student> studentTreeSetById = new TreeSet<>(new CompareById());
        studentTreeSetById.add(new Student(101, "Alice", 3.7));
        studentTreeSetById.add(new Student(102, "Bob", 3.9));
        studentTreeSetById.add(new Student(103, "Charlie", 3.5));
        studentTreeSetById.add(new Student(101, "David", 3.8)); 

        // Print the students in the TreeSet (sorted by ID)
        System.out.println("TreeSet - Unique Students (Sorted by ID):");
        for (Student student : studentTreeSetById) {
            System.out.println(student);
        }

        System.out.println();

        // Create a TreeSet to store unique students based on their names
        TreeSet<Student> studentTreeSetByName = new TreeSet<>(new CompareByName()); // burada öncekilerin aksine comparable vermek zorundayız çünkü student'ın neyi ile kıyaslayacağını bilmiyo. içind compare metodu override edilmiş bir sınıf verilmesi gereklidir.
        studentTreeSetByName.add(new Student(101, "Alice", 3.7));
        studentTreeSetByName.add(new Student(102, "Bob", 3.9));
        studentTreeSetByName.add(new Student(103, "Charlie", 3.5));
        studentTreeSetByName.add(new Student(101, "David", 3.8)); 

        // Print the students in the TreeSet (sorted by name)
        System.out.println("TreeSet - Unique Students (Sorted by Name):");
        for (Student student : studentTreeSetByName) {
            System.out.println(student);
        }

        System.out.println();

        // Create a TreeSet to store unique students based on their GPAs
        TreeSet<Student> studentTreeSetByGpa = new TreeSet<>(new CompareByGpa());
        studentTreeSetByGpa.add(new Student(101, "Alice", 3.7));
        studentTreeSetByGpa.add(new Student(102, "Bob", 3.9));
        studentTreeSetByGpa.add(new Student(103, "Charlie", 3.5));
        studentTreeSetByGpa.add(new Student(101, "David", 3.7)); // Duplicate gpa, but will not be added

        // Print the students in the TreeSet (sorted by GPA)
        System.out.println("TreeSet - Unique Students (Sorted by GPA):");
        for (Student student : studentTreeSetByGpa) {
            System.out.println(student);
        }
    }
}

class Example_Advanced {
    public static void main(String[] args) {
        // Create an array of students
        Student[] students = {
                new Student(101, "Alice", 3.7),
                new Student(102, "Bob", 3.9),
                new Student(103, "Charlie", 3.5),
                new Student(104, "David", 3.8)
        };

        // Use the array of students to populate HashSet and TreeSet
        Set<Student> studentHashSet = new HashSet<>(Arrays.asList(students));
        Set<Student> studentTreeSetById = new TreeSet<>(new CompareById());
        studentTreeSetById.addAll(Arrays.asList(students));

        // Print students in HashSet
        System.out.println("HashSet - Unique Students:");
        for (Student student : studentHashSet) {
            System.out.println(student);
        }

        System.out.println();

        // Print students in TreeSet (sorted by ID)
        System.out.println("TreeSet - Unique Students (Sorted by ID):");
        for (Student student : studentTreeSetById) {
            System.out.println(student);
        }
    }
}
```

### HashMap
```java
import java.util.HashMap;

// Class representing a User
class User {
    private int id;
    private String username;
    private String email;

    public User(int id, String username, String email) {
        this.id = id;
        this.username = username;
        this.email = email;
    }

    public int getId() {
        return id;
    }

    public String getUsername() {
        return username;
    }

    public String getEmail() {
        return email;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}

class WebAppExample {
    public static void main(String[] args) {
        // Create a HashMap to store session information for each user
        HashMap<Integer, User> sessionMap = new HashMap<>();

        // Simulate users logging in and creating sessions
        User user1 = new User(101, "alice", "alice@example.com");
        int session1 = user1.hashCode();
        sessionMap.put(session1, user1);

        User user2 = new User(102, "bob", "bob@example.com");
        int session2 = user2.hashCode();
        sessionMap.put(session2, user2);

        // Simulate user accessing their session
        System.out.println("User associated with session " + session1 + ": " + sessionMap.get(session1));
        System.out.println("User associated with session " + session2 + ": " + sessionMap.get(session2));

        // Simulate user logging out and removing session
        sessionMap.remove(session1);
        System.out.println("Session " + session1 + " removed.");

        // Check if session exists after logout
        if (sessionMap.containsKey(session1)) {
            System.out.println("User associated with session " + session1 + ": " + sessionMap.get(session1));
        } else {
            System.out.println("Session " + session1 + " is not active.");
        }
    }
}
```

### ArrayList vs LinkedList
```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;
import java.util.LinkedList;

class Main {
    public static void main(String[] args) {
        int numOperations = 100000; // Number of operations (can be adjusted)

        // Insert/deletion heavy load speed between ArrayList and LinkedList
        long startTimeArrayListInsertDelete = System.currentTimeMillis();
        insertDeleteHeavyLoad(new ArrayList<>(), numOperations);
        long endTimeArrayListInsertDelete = System.currentTimeMillis();
        long timeArrayListInsertDelete = endTimeArrayListInsertDelete - startTimeArrayListInsertDelete;

        long startTimeLinkedListInsertDelete = System.currentTimeMillis();
        insertDeleteHeavyLoad(new LinkedList<>(), numOperations);
        long endTimeLinkedListInsertDelete = System.currentTimeMillis();
        long timeLinkedListInsertDelete = endTimeLinkedListInsertDelete - startTimeLinkedListInsertDelete;

        // Retrieval heavy load speed between ArrayList and LinkedList
        long startTimeArrayListRetrieval = System.currentTimeMillis();
        retrievalHeavyLoad(new ArrayList<>(), numOperations);
        long endTimeArrayListRetrieval = System.currentTimeMillis();
        long timeArrayListRetrieval = endTimeArrayListRetrieval - startTimeArrayListRetrieval;

        long startTimeLinkedListRetrieval = System.currentTimeMillis();
        retrievalHeavyLoad(new LinkedList<>(), numOperations);
        long endTimeLinkedListRetrieval = System.currentTimeMillis();
        long timeLinkedListRetrieval = endTimeLinkedListRetrieval - startTimeLinkedListRetrieval;

        // Output results
        System.out.println("Insert/deletion heavy load:");
        System.out.println("ArrayList time: " + timeArrayListInsertDelete + " ms");
        System.out.println("LinkedList time: " + timeLinkedListInsertDelete + " ms");

        System.out.println("\nRetrieval heavy load:");
        System.out.println("ArrayList time: " + timeArrayListRetrieval + " ms");
        System.out.println("LinkedList time: " + timeLinkedListRetrieval + " ms");
    }

    // Method to simulate insert/deletion heavy load
    public static void insertDeleteHeavyLoad(List<Integer> list, int numOperations) {
        for (int i = 0; i < numOperations; i++) {
            list.add(i); // Insert operation
        }

        for (int i = 0; i < numOperations / 2; i++) {
            list.remove(0); // Deletion operation
        }
    }

    // Method to simulate retrieval heavy load
    public static void retrievalHeavyLoad(List<Integer> list, int numOperations) {
        for (int i = 0; i < numOperations; i++) {
            list.add(i);
        }

        for (int i = 0; i < numOperations; i++) {
            int value = list.get(i); // Retrieval operation
        }
    }
}
```

### PriorityQueue
```java
import java.util.PriorityQueue;

// Task class representing a task in a project
class Task implements Comparable<Task> {
    private String name;
    private int priority;

    public Task(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }

    public String getName() {
        return name;
    }

    public int getPriority() {
        return priority;
    }

    @Override
    public int compareTo(Task other) {
        return Integer.compare(this.priority, other.priority);
    }
}

class ProjectManager {
    public static void main(String[] args) {
        // Create a PriorityQueue for managing tasks
        PriorityQueue<Task> taskQueue = new PriorityQueue<>();  // Önemli bilgi: pq'lar için natural order küçükten büyüğe şeklindedir. İçindeki type basitse tabi; integer, string, vs. Mesela integer tür tutan pq'da minheap olduğundan 50,30,10 şeklinde dizilirler.

        // Add tasks to the PriorityQueue
        taskQueue.add(new Task("Implement feature X", 2)); // Higher priority
        taskQueue.add(new Task("Fix critical bug", 1));   // Highest priority
        taskQueue.add(new Task("Write documentation", 3)); // Lower priority

        // Process tasks in priority order
        while (!taskQueue.isEmpty()) {
            Task task = taskQueue.poll();
            System.out.println("Processing task: " + task.getName() + " (Priority: " + task.getPriority() + ")");
        }
    }
}

```

### Karışık Örnekler

```java
public boolean removeMiddle(int index) {
    if (index <= 0 || index >= size - 1) return false;
    
    Node curr = head;
    for (int i = 0; i < index - 1; i++) {
        curr = curr.next;
    }
    curr.next = curr.next.next;
    

    return true;
}
```

```java
import java.util.*;

public class CollectionTrace {
    public static void main(String[] args) {
        Set<String> set1 = new LinkedHashSet<>();
        set1.add("Muz");
        set1.add("Elma");
        set1.add("Çilek");
        set1.add("Muz");

        Set<String> set2 = new TreeSet<>(set1);
        
        System.out.println(set1);
        System.out.println(set2);
    }
}
// output: [muz elma çilek] ve [çilek elma muz]
```

```java
import java.util.PriorityQueue;

class Araba {
    String marka;
    Araba(String m) { this.marka = m; }
}

public class Test {
    public static void main(String[] args) {
        PriorityQueue<Araba> q = new PriorityQueue<>();
        q.add(new Araba("BMW"));
        q.add(new Araba("Audi"));
        System.out.println(q.peek().marka);
    }
}
//output: classcastexception atar çünkü priorityqueue içersiine bi comparable interface'i bekler ne ile compare edeceğini bilebilmek için. soruyu düzeltebilmek için 2 yol var:
// 1. Araba sınıfı comparable implement etmeli: class Araba implements Comparable<Araba>{} şeklinde. <Araba> kısmı oldu olmak zorundadır!!!
// 2. PriorityQueue<Araba> q = new PriorityQueue<>(new Comparable<Araba>(){
//      public int compareTo(){
//          return a1.marka.compareTo(a2.marka);
//      }
// }); şeklinde anonymous class olarak tanımlanmalıdır compare kuralı.
```

```java
Map<String, Integer> map = new HashMap<>();
String s1 = "Java";
String s2 = new String("Java");

map.put(s1, 1);
map.put(s2, 2);

System.out.println(map.get("Java"));
System.out.println(map.size());
// output: 2 ve 1 çünkü map key olarak string tutmakta burda da hiç fark etmiyor string nesnesi mi literal olarak mı nasıl yollandığı aynı adreste tutulur. size artmaz dolayısı ile s2 ile.
```

```java
class Anahtar {
    int id;
    Anahtar(int id) { this.id = id; }
    // equals() ve hashCode() yazılmadı!
}

HashSet<Anahtar> set = new HashSet<>();
set.add(new Anahtar(1));
set.add(new Anahtar(1));

System.out.println(set.size());
// output: 2 çünkü iki farklı anahtar objesi eklenmiştir. equals() ve hashCode() yazmaya gerek yok sınıf kendinikini kullanır override edilmemiş olur sadece.
```

```java
// Bir HashMap<String, Integer> yapısında, Value değeri (Integer) 50'den küçük olan tüm kayıtları silmek istiyoruz. Aşağıdaki kodlar bunu yapabilmekte.

// 1
Iterator<Map.Entry<String, Integer>> iter = map.entrySet().iterator();  // bir yapıyı iteartor ile gezmek en mantıklısı çünkü for ile gezip tek tek silecek olursan pointer nerede olduğunu karıştırabilir sıkıntısal o da
while(iter.hasNext())
    if (iter.next().getValue() < 50)
        iter.remove();

// 2 
map.values().removeIf(v -> v < 50);
```

```java
// Map.Entry kullanımı:
Map<String, Integer> boylar = new HashMap<>();
boylar.put("Ali", 185);
boylar.put("Veli", 178);

// entrySet() bize Set<Map.Entry<K, V>> döner.
for (Map.Entry<String, Integer> zarf : boylar.entrySet()) {
    String isim = zarf.getKey();     // Anahtarı al
    Integer boy = zarf.getValue();   // Değeri al

    // güncellenecekse
    //zarf.setValue(2);
    //diğer metotlar
    /**
     * setValue(V value)
     * 
     * getKey()
     * getValue()
     * 
     * iterator içindeysek remove()
     * 
    */
    
    System.out.println(isim + " kişisinin boyu: " + boy);
}

// azönceki örnekteki gibi iterator de kullanılabilir

for (String key : map.keySet()) {
    Integer value = map.get(key); // keySet() kullanımı daha yavaştır, gerek yko.
}
```

```java
// şöyle bi mevzu var ki hashCode() daima 7 dönmekte. :D nolur?
Map<BozukSinif, String> map = new HashMap<>();
map.put(new BozukSinif("A"), "Değer 1");
map.put(new BozukSinif("B"), "Değer 2");

System.out.println(map.size());
System.out.println(map.get(new BozukSinif("A")));

//output: collision gerçekleşeceğinden dolayı linked list şeklinde value'Ları dizer. ancak keyleri equals'ladığı anda değer 1 return eder diğeri kaybolur :(, size 2 bu arada


```


## GUI

### Örnek 1
```java
import javax.swing.*;
import javax.swing.event.MenuListener;

import java.awt.*;
import java.awt.event.*;

/**
 * Component
 *      Container
 *          Window
 *              Frame
 *                  JFrame
 *              Dialog
 *                  JDialog
 *          JComponent
 *              JText
 *                  JTextField  
 *                  JTextArea 
 *              JScrollbar
 *              JPanel
 *              JOptionPane
 *              JMenu
 *              JList 
 *              JLabel 
 *              JComboBox
 *              AbstractButton
 *                  JButton
 */

public class Main{
    
    public static void main(String[] args) {
        FirstWindow w = new FirstWindow();
        w.setVisible(true);
    }
}
class FirstWindow extends JFrame implements ActionListener{
    public final static int WIDTH = 800;
    public final static int HEIGHT = 600;
    public final static int SMALL_WIDTH = 200;
    public final static int SMALL_HEIGHT = 200;
    
    public JPanel redPanel = new JPanel();
    public JPanel greenPanel = new JPanel();
    public JPanel bluePanel = new JPanel();

    public FirstWindow(){
        this(Color.PINK);
    }
    public FirstWindow(Color color){
        super("no charge for color");
        setSize(WIDTH, HEIGHT);
        setTitle("First Window Class");
        setDefaultCloseOperation(JFrame.DO_NOTHING_ON_CLOSE);
        addWindowListener(new CheckOnExit());
        getContentPane().setBackground(color);

        JButton endButton = new JButton("Click to end program.");
        endButton.addActionListener(new EndingListener()); // aşağıdaki şekilde de olabilir:
        /*
        EndingListener buttonEar = new EndingListener();
        endButton.addActionListener(buttonEar);
        */
        
        JLabel l1 = new JLabel("first label"/*, new ImageIcon("C:\\Users\\kerem\\Desktop\\KEREM\\ÖZEL\\vesikalık.png"), SwingConstants.LEFT*/);
        JLabel l2 = new JLabel("second label");
        JLabel l3 = new JLabel("third label");
        // BORDER LAYOUT
        setLayout(new BorderLayout());
        add(l1, BorderLayout.NORTH);
        add(endButton, BorderLayout.SOUTH);

        // FLOW LAYOUT
        /*setLayout(new FlowLayout());
        add(l1);
        add(l2);
        add(l3);
        add(endButton);*/

        // GRID LAYOUT
        /*setLayout(new GridLayout(2,2, 2, 2));
        add(l1);
        add(l2);
        add(l3);
        add(endButton);*/

        // RENK PANELLERİ
        JPanel biggerPanel = new JPanel();
        biggerPanel.setLayout(new GridLayout(1,3));

        redPanel.setBackground(Color.RED.darker());
        greenPanel.setBackground(Color.GREEN.darker());
        bluePanel.setBackground(Color.BLUE.darker());

        biggerPanel.add(redPanel);
        biggerPanel.add(greenPanel);
        biggerPanel.add(bluePanel);

        add(biggerPanel, BorderLayout.CENTER);

        // BUTTON PANELLERİ
        JPanel buttonPanel = new JPanel();
        buttonPanel.setBackground(Color.LIGHT_GRAY);
        buttonPanel.setLayout(new FlowLayout());

        JButton redButton = new JButton("Red");
        redButton.setBackground(Color.RED);
        JButton blueButton = new JButton("Blue");
        blueButton.setBackground(Color.BLUE);
        JButton greenButton = new JButton("Green");
        greenButton.setBackground(Color.GREEN);
        redButton.addActionListener(this);      // bunu yazdığımız sınıfın implement ActionListener etmesi gereken. kalabalık ve karmarış tek mettota handle edilmesini gerektirir. yeniden kullanım olmaz ancak yukarıdaki enbutton için ayrı bir java sınıfı kullanılmıştır o yüzden tekrar kullanılabilir, daha özel yapıda, harici bir sınıf gerektirir.
        greenButton.addActionListener(this);
        blueButton.addActionListener(this);
        buttonPanel.add(redButton);
        buttonPanel.add(blueButton);
        buttonPanel.add(greenButton);

        add(buttonPanel, BorderLayout.WEST);

        // MENU BAR
        JMenu fileMenu = new JMenu("File");
        fileMenu.add(new JMenuItem("New"));
        fileMenu.add(new JMenuItem("Open"));
        fileMenu.add(new JMenuItem("Close"));

        JMenu editMenu = new JMenu("Edit");
        editMenu.add(new JMenuItem("Undo"));
        editMenu.add(new JMenuItem("Redo"));
        editMenu.add(new JMenuItem("Cut"));

        JMenuBar menuBar = new JMenuBar();
        menuBar.add(fileMenu);
        menuBar.add(editMenu);

        setJMenuBar(menuBar);

        ActionListener menuListener = new ActionListener() {
            public void actionPerformed(ActionEvent e){
                displayInfo(e);
            }
        };

        for(int i=0; i<menuBar.getMenuCount(); i++){
            JMenu menu = menuBar.getMenu(i);
            for(int j=0; j<menu.getItemCount(); j++){
                JMenuItem menuItem = menu.getItem(j);
                menuItem.addActionListener(menuListener);
            }
        }

        //TEXT AREA
        JTextArea textArea = new JTextArea("asda", 15,30);
        //textArea.setEditable(false);
        add(textArea, BorderLayout.EAST);


        //SCROLL BAR
        JScrollPane scrolledText = new JScrollPane(textArea);
        scrolledText.setHorizontalScrollBarPolicy(JScrollPane.HORIZONTAL_SCROLLBAR_AS_NEEDED);
        scrolledText.setVerticalScrollBarPolicy(JScrollPane.VERTICAL_SCROLLBAR_ALWAYS);
        add(scrolledText);
        
    }
    public void actionPerformed(ActionEvent e){
        String buttonString = e.getActionCommand();
        if(buttonString.equals("Red"))
            if(redPanel.getBackground() == Color.WHITE)
                redPanel.setBackground(Color.RED);    
            else 
                redPanel.setBackground(Color.WHITE);
        else if(buttonString.equals("Green"))
            if(greenPanel.getBackground() == Color.WHITE)
                greenPanel.setBackground(Color.GREEN);    
            else 
                greenPanel.setBackground(Color.WHITE);
        else if(buttonString.equals("Blue"))
            if(bluePanel.getBackground() == Color.WHITE)
                bluePanel.setBackground(Color.BLUE);    
            else 
                bluePanel.setBackground(Color.WHITE);
        else
            System.out.println("Error.");
    }
    private static void displayInfo(ActionEvent e){
        JMenuItem menuItem = (JMenuItem) e.getSource();
        Component parent = menuItem.getParent();
        String menuText = "";
        if(parent instanceof JPopupMenu){
            JPopupMenu popupMenu = (JPopupMenu) parent;
            Component invoker = popupMenu.getInvoker();
            if(invoker instanceof JMenu){
                JMenu menu = (JMenu) invoker;
                menuText = menu.getText();
            }
        }
        else if(parent instanceof JMenu){
            JMenu menu = (JMenu) parent;
            menuText = menu.getText();
        }
        String operationText = menuItem.getText();
        long time = e.getWhen();
        String action = e.getActionCommand();
        String info = "Menu: " + menuText + ", Operation: " + operationText + ", Time: " + time + ", Action: " + action;
        JOptionPane.showMessageDialog(null, info);
    }
    // inner class confirm checker 
    class CheckOnExit implements WindowListener{
        public void windowOpened(WindowEvent e){}

        public void windowClosing(WindowEvent e){
            ConfirmWindow checkers = new ConfirmWindow();
            checkers.setVisible(true);
        }
        
        public void windowClosed(WindowEvent e){}
        public void windowIconified(WindowEvent e){}
        public void windowDeiconified(WindowEvent e){}
        public void windowActivated(WindowEvent e){}
        public void windowDeactivated(WindowEvent e){}
    }
    class ConfirmWindow extends JFrame implements ActionListener{
        public ConfirmWindow(){
            setSize(SMALL_WIDTH, SMALL_HEIGHT);
            getContentPane().setBackground(Color.YELLOW);
            setLayout(new BorderLayout());
            JLabel confirmLabel = new JLabel("Are you sure to exit?");
            add(confirmLabel, BorderLayout.CENTER);

            JPanel buttonPanel = new JPanel();
            buttonPanel.setBackground(Color.ORANGE);
            buttonPanel.setLayout(new FlowLayout());

            JButton exitButton = new JButton("Yes");
            // exitButton.setIcon(new ImageIcon("C:\\Users\\kerem\\Desktop\\KEREM\\ÖZEL\\vesikalık.png"));
            exitButton.addActionListener(this);
            buttonPanel.add(exitButton);

            JButton cancelButton = new JButton("No");
            cancelButton.addActionListener(this);
            buttonPanel.add(cancelButton);

            add(buttonPanel, BorderLayout.SOUTH);
        }
        public void actionPerformed(ActionEvent e){
            String command = e.getActionCommand();
            
            if(command.equals("Yes"))
                System.exit(0);
            else
                dispose();
        }
    }
}

class EndingListener implements ActionListener{
    public void actionPerformed(ActionEvent e){
        System.exit(0);
    }
}
```

### Örnek 2
```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;;

public class Face extends JFrame{
    public static final int WINDOWS_HEIGHT = 400;
    public static final int WINDOWS_WIDTH = 400;

    public static final int FACE_DIAMETER = 200;
    public static final int X_FACE = 100;
    public static final int Y_FACE = 100;

    public static final int EYE_WIDTH = 20;
    public static final int X_RIGHT_EYE = X_FACE + 55;
    public static final int Y_RIGHT_EYE = Y_FACE + 60;
    public static final int X_LEFT_EYE = X_FACE + 130;
    public static final int Y_LEFT_EYE = Y_FACE + 60;

    public static final int MOUTH_WIDTH = 100;
    public static final int X_MOUTH = X_FACE + 50;
    public static final int Y_MOUTH = Y_FACE + 150;

    private boolean wink;

    public static void main(String[] args) {
        Face drawing = new Face();
        drawing.setVisible(true);
    }
    public Face(){
        super("First graphics program");
        setSize(WINDOWS_WIDTH, WINDOWS_HEIGHT);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        getContentPane().setBackground(Color.white.darker());
        setLayout(new BorderLayout());

        JButton winkButton = new JButton("Wink");
        winkButton.addActionListener(new WinkAction());
        add(winkButton, BorderLayout.SOUTH);
        wink = false;
    }
    public void paint(Graphics g){
        /**
         * drawString(String str, intxBaselineLeft, intyBaselineLeft);
         * drawLine(intx1, inty1, intx2, inty2);
         * drawPolyline(int[] xPoints, int[] yPoints, intnumPoint);
         * drawRect(intxTopLeft, intyTopLeft, intwidth, intheight);
         * drawOval(intxTopLeft, intyTopLeft, intwidth, intheight);
         * drawArc(intxTopLeft, intyTopLeft, intwidth, intheight, intstartAngle, intarcAngle);
         * 
         * fillRect(intxTopLeft, intyTopLeft, intwidth, intheight);
         * fillOval(intxTopLeft, intyTopLeft, intwidth, intheight);
         * fillArc(intxTopLeft, intyTopLeft, intwidth, intheight, intstartAngle, intarcAngle);
         * fillRoundRect(intxTopLeft, intyTopLeft, intwidth, intheight, intarcWidth, intarcHeight)
         * fillPolygon(int[] xPoints, int[] yPoints, intnumPoint);
         * 
         * drawImage(Image img, intxTopLeft, intyTopLeft, ImageObserverobs); // draw image with its size
         * drawImage(Image img, intxTopLeft, intyTopLeft, intwidth, intheight, ImageObservero); // resize image on screen
         * drawImage(Image img, intdestX1, intdestY1, intdestX2, intdestY2,int srcX1, intsrcY1, intsrcX2, intsrcY2, ImageObserverobserver)
         * 
         * 
         */
        super.paint(g);
        g.drawOval(X_FACE, Y_FACE, FACE_DIAMETER, FACE_DIAMETER);
        //draw eyes
        g.fillOval(X_RIGHT_EYE, Y_RIGHT_EYE, EYE_WIDTH, EYE_WIDTH);
        if(wink){
            g.drawLine(X_LEFT_EYE, Y_LEFT_EYE, X_LEFT_EYE + EYE_WIDTH, Y_LEFT_EYE);
        } else {
            g.fillOval(X_LEFT_EYE, Y_LEFT_EYE, EYE_WIDTH, EYE_WIDTH);
        }
        g.drawLine(X_LEFT_EYE, Y_LEFT_EYE, X_LEFT_EYE + EYE_WIDTH, Y_LEFT_EYE);
        
        //draw mouth
        //g.drawLine(X_MOUTH, Y_MOUTH, X_MOUTH + MOUTH_WIDTH, Y_MOUTH);
        g.drawArc(X_MOUTH, Y_MOUTH, MOUTH_WIDTH, 20, 180, 180);
    }
    private class WinkAction implements ActionListener{
        public void actionPerformed(ActionEvent e){
            wink = !wink;
            repaint();
        }
    }
}
```

### Örnek 3
```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class BasicCalculator {
    private JFrame frame;
    private JTextField textField;
    private JButton[] numberButtons;
    private JPanel digitsPanel;
    private JPanel operationsPanel;

    private double num1, num2, result;
    private char operator;

    public BasicCalculator() {
        frame = new JFrame("Esnaf Calculator");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(300, 400);
        frame.setLayout(new BorderLayout());

        textField = new JTextField();
        // text field change size
        textField.setPreferredSize(new Dimension(300, 100));
        // text field not editable
        textField.setEditable(false);
        // text field right align
        textField.setHorizontalAlignment(JTextField.RIGHT);
        // change font
        textField.setFont(new Font("Arial", Font.BOLD, 30));
        // text field change color
        textField.setBackground(Color.WHITE);
        textField.setForeground(Color.BLACK);


        numberButtons = new JButton[10];
        for (int i = 0; i < 10; i++) {
            numberButtons[i] = new JButton(String.valueOf(i));
            numberButtons[i].addActionListener(new ActionListener() {
                public void actionPerformed(ActionEvent e) {
                    String currentText = textField.getText();
                    textField.setText(currentText + ((JButton) e.getSource()).getText());
                }
            });
        }

        digitsPanel = new JPanel();
        digitsPanel.setLayout(new GridLayout(4, 3, 10, 10));
        for (int i = 1; i < 10; i++) {
            digitsPanel.add(numberButtons[i]);
        }
        digitsPanel.add(numberButtons[0]);
        digitsPanel.add(createButton("C"));
        digitsPanel.add(createButton("="));

        addButton("+");
        addButton("-");
        addButton("*");
        addButton("/");

        frame.add(textField, BorderLayout.NORTH);
        frame.add(digitsPanel, BorderLayout.CENTER);
        frame.add(operationsPanel, BorderLayout.EAST);

        frame.setVisible(true);
    }

    private void addButton(String label) {
        JButton button = new JButton(label);
        button.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                String command = ((JButton) e.getSource()).getText();
                if (!command.equals("=") && !command.equals("C")) {
                    operator = command.charAt(0);
                    num1 = Double.parseDouble(textField.getText());
                    textField.setText("");
                } else if (command.equals("C")) {
                    textField.setText("");
                } else {
                    num2 = Double.parseDouble(textField.getText());
                    switch (operator) {
                        case '+':
                            result = num1 + num2;
                            break;
                        case '-':
                            result = num1 - num2;
                            break;
                        case '*':
                            result = num1 * num2;
                            break;
                        case '/':
                            if (num2 != 0)
                                result = num1 / num2;
                            else
                                result = Double.NaN; // Indicate error for division by zero
                            break;
                    }
                    textField.setText(String.valueOf(result));
                }
            }
        });
        if (operationsPanel == null) {
            operationsPanel = new JPanel();
            operationsPanel.setLayout(new GridLayout(4, 1, 10, 10));
        }
        operationsPanel.add(button);
    }

    private JButton createButton(String label) {
        JButton button = new JButton(label);
        button.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                String command = ((JButton) e.getSource()).getText();
                if (command.equals("C")) {
                    textField.setText("");
                } else if (command.equals("=")) {
                    // Perform calculation
                    num2 = Double.parseDouble(textField.getText());
                    switch (operator) {
                        case '+':
                            result = num1 + num2;
                            break;
                        case '-':
                            result = num1 - num2;
                            break;
                        case '*':
                            result = num1 * num2;
                            break;
                        case '/':
                            if (num2 != 0)
                                result = num1 / num2;
                            else
                                result = Double.NaN; // Indicate error for division by zero
                            break;
                    }
                    textField.setText(String.valueOf(result));
                }
            }
        });
        return button;
    }

    public static void main(String[] args) {
        new BasicCalculator();
    }
}
```

### Örnek 4
```java
import javax.swing.*;
import javax.swing.event.MouseInputListener;
import java.awt.*;
import java.awt.event.*;
import java.util.ArrayList;

public class PaintBrush {
    public static void main(String[] args) {
        new MainFrame();
    }
}
class MainFrame extends JFrame implements MouseInputListener {
    ArrayList<Shapes> shapes = new ArrayList<>();
    Shapes newShape = null;
    Graphics g = null;
    MouseEvent e = null;
    int currRenk = 7;
    int currMod,currX,currY,thickness,startY,startX,transportColor,fromX,fromY,toX,toY = 0;
    boolean tasinabilirObje,dragging,transport = false;
    String transportType = "";

    JPanel ust_panel = new JPanel();
    JPanel renk_paneli = new JPanel();
    JPanel mavi_renk = new JPanel();
    JPanel kirmizi_renk = new JPanel();
    JPanel yesil_renk = new JPanel();
    JPanel sari_renk = new JPanel();
    JPanel turuncu_renk = new JPanel();
    JPanel mor_renk = new JPanel();
    JPanel siyah_renk = new JPanel();
    JPanel mod_paneli = new JPanel();
    JPanel bar_paneli = new JPanel();
    JPanel cizim_paneli = new JPanel();

    MainFrame(){
        setSize(1280,720);
        setDefaultCloseOperation(EXIT_ON_CLOSE);

        renk_paneli.setName("renk paneli");
        renk_paneli.setBackground(Color.white);
        renk_paneli.setLayout(new FlowLayout());

        mavi_renk.setBackground(Color.blue);
        kirmizi_renk.setBackground(Color.red);
        yesil_renk.setBackground(Color.green);
        sari_renk.setBackground(Color.yellow);
        turuncu_renk.setBackground(Color.orange);
        mor_renk.setBackground(Color.magenta);
        siyah_renk.setBackground(Color.black);

        mavi_renk.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                currRenk = 1;
            }
        });
        kirmizi_renk.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                currRenk = 2;
            }
        });
        yesil_renk.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                currRenk = 3;
            }
        });
        sari_renk.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                currRenk = 4;
            }
        });
        turuncu_renk.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                currRenk = 5;
            }
        });
        mor_renk.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                currRenk = 6;
            }
        });
        siyah_renk.addMouseListener(new MouseAdapter() {
            @Override
            public void mouseClicked(MouseEvent e) {
                currRenk = 7;
            }
        });

        mavi_renk.setPreferredSize(new Dimension(60,30));
        kirmizi_renk.setPreferredSize(new Dimension(60,30));
        yesil_renk.setPreferredSize(new Dimension(60,30));
        turuncu_renk.setPreferredSize(new Dimension(60,30));
        sari_renk.setPreferredSize(new Dimension(60,30));
        siyah_renk.setPreferredSize(new Dimension(60,30));
        mor_renk.setPreferredSize(new Dimension(60,30));

        renk_paneli.add(mavi_renk);
        renk_paneli.add(kirmizi_renk);
        renk_paneli.add(yesil_renk);
        renk_paneli.add(sari_renk);
        renk_paneli.add(turuncu_renk);
        renk_paneli.add(mor_renk);
        renk_paneli.add(siyah_renk);

        ust_panel.setLayout(new BorderLayout());
        ust_panel.add(renk_paneli,BorderLayout.NORTH);

        mod_paneli.setBackground(Color.white);
        mod_paneli.setLayout(new FlowLayout());

        JButton dikdortgen = new JButton("Rectangle");
        JButton oval = new JButton("Circle");
        JButton kalem = new JButton("Draw");
        JButton tasi = new JButton("Move");

        dikdortgen.setBackground(Color.lightGray);
        oval.setBackground(Color.lightGray);
        kalem.setBackground(Color.lightGray);
        tasi.setBackground(Color.lightGray);

        dikdortgen.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                currMod=1;
            }
        });
        oval.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                currMod=2;
            }
        });
        kalem.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                currMod=3;
            }
        });
        tasi.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                currMod=4;
            }
        });

        mod_paneli.add(dikdortgen);
        mod_paneli.add(oval);
        mod_paneli.add(kalem);
        //move does not work properly
        //mod_paneli.add(tasi);

        ust_panel.add(mod_paneli,BorderLayout.CENTER);
        ust_panel.add(bar_paneli,BorderLayout.SOUTH);

        bar_paneli.setBackground(Color.blue);
        bar_paneli.setPreferredSize(new Dimension(720,5));

        cizim_paneli.setBackground(Color.white);
        cizim_paneli.addMouseListener(this);
        cizim_paneli.addMouseMotionListener(this);

        add(ust_panel,BorderLayout.NORTH);
        add(cizim_paneli,BorderLayout.CENTER);

        setVisible(true);
    }

    void setGraphics(Graphics newG){
        g = newG;
    }
    public void setMouse(MouseEvent newE){
        this.e = newE;
    }

    public void paint(Graphics g){
        if(currMod==0) {
            super.paint(g);
            return;
        }
        setGraphics(g);
        switch (currRenk) {
            case 1 -> g.setColor(Color.blue);
            case 2 -> g.setColor(Color.red);
            case 3 -> g.setColor(Color.green);
            case 4 -> g.setColor(Color.yellow);
            case 5 -> g.setColor(Color.orange);
            case 6 -> g.setColor(Color.magenta);
            case 7 -> g.setColor(Color.black);
            case 8 -> g.setColor(Color.white);
        }

        if(currMod==1){
            newShape = new Shapes(startX+10,startY+115,currX-startX,currY-startY,"kare",currRenk);
            g.fillRect(startX+10,startY+115,currX-startX,currY-startY);
            shapes.add(newShape);
        }
        else if (currMod == 2) {
            newShape = new Shapes(startX+10,startY+115,currX-startX,currY-startY,"oval",currRenk);
            g.fillOval(startX+10,startY+115,currX-startX,currY-startY);
            shapes.add(newShape);
        }
        else if (currMod == 3) {
            g.fillOval(currX+10,currY+115,10,10);
        }
        else if(currMod == 4 && tasinabilirObje){
            g.setColor(Color.white);
            if(this.newShape.type.equals("kare")) {
                transportType = "kare";
                g.fillRect(this.newShape.startX, this.newShape.startY, this.newShape.width, this.newShape.height);
            }
            else if(this.newShape.type.equals("oval")) {
                transportType = "oval";
                g.fillOval(this.newShape.startX, this.newShape.startY, this.newShape.width, this.newShape.height);
            }
            switch (currRenk) {
                case 1 -> g.setColor(Color.blue);
                case 2 -> g.setColor(Color.red);
                case 3 -> g.setColor(Color.green);
                case 4 -> g.setColor(Color.yellow);
                case 5 -> g.setColor(Color.orange);
                case 6 -> g.setColor(Color.magenta);
                case 7 -> g.setColor(Color.black);
                case 8 -> g.setColor(Color.white);
            }
            tasinabilirObje = false;
            transport = true;
        }
        else if(currMod == 4 && transport){
            if(this.newShape.color == 8)    this.newShape.color = currRenk;
            switch (this.newShape.color){
                case 1 -> g.setColor(Color.blue);
                case 2 -> g.setColor(Color.red);
                case 3 -> g.setColor(Color.green);
                case 4 -> g.setColor(Color.yellow);
                case 5 -> g.setColor(Color.orange);
                case 6 -> g.setColor(Color.magenta);
                case 7 -> g.setColor(Color.black);
            }
            switch (transportType){
                case "kare" -> {
                    g.fillRect(toX+10,toY+115,this.newShape.width,this.newShape.height);
                    this.newShape = new Shapes(toX+10,toY+115,this.newShape.width,this.newShape.height, "kare",this.newShape.color);
                }
                case "oval" -> {
                    g.fillOval(toX+10,toY+115,this.newShape.width,this.newShape.height);
                    this.newShape = new Shapes(toX+10,toY+115,this.newShape.width,this.newShape.height, "oval",this.newShape.color);
                }
            }
            currRenk = this.newShape.color;
            shapes.add(newShape);
            transport=false;
        }
    }
    @Override
    public void mousePressed(MouseEvent e) {
        currX = e.getX();
        currY = e.getY();
        setMouse(e);
        if(e.getY()>=0 && currMod != 0) dragging  = true;
        if (currMod==4){
            for(int i=0; i< shapes.size(); i++){
                if(currX+10>shapes.get(i).startX && currX+10<(shapes.get(i).startX+shapes.get(i).width) && currY+115>shapes.get(i).startY && currY+115<(shapes.get(i).startY+shapes.get(i).height) && shapes.get(i).color<8){
                    this.newShape = shapes.get(i);
                    this.transportColor = this.newShape.color;
                    shapes.remove(shapes.get(i));
                    tasinabilirObje=true;
                    fromX = currX;
                    fromY = currY;
                    break;
                }
            }
            repaint();
        }
    }
    @Override
    public void mouseDragged(MouseEvent e) {
        currX = e.getX();
        currY = e.getY();
        if(dragging){
            if(thickness==0){
                startX = currX;
                startY = currY;
                thickness++;
            }
        }
        if(currMod == 3){
            repaint();
        }
    }
    @Override
    public void mouseReleased(MouseEvent e) {
        toX = e.getX();
        toY = e.getY();
        if(startX>currX){
            int tmp = startX+currX;
            startX = tmp - startX;
            currX = tmp - currX;
        }
        if(startY>currY){
            int tmp = startY+currY;
            startY = tmp - startY;
            currY = tmp - currY;
        }
        if(dragging && currMod != 3){
            repaint();
        }
        else if(transport && currMod == 4){
            repaint();
        }
        thickness=0;
        dragging = false;

    }
    @Override
    public void mouseExited(MouseEvent e) {
        dragging = false;
        transport = false;
    }
    @Override
    public void mouseMoved(MouseEvent e) {}
    @Override
    public void mouseEntered(MouseEvent e) {}
    @Override
    public void mouseClicked(MouseEvent e) {}
}
class Shapes{
    int startX,startY,width,height,color;
    String type;
    Shapes(int startX, int startY, int width, int height, String type, int color) {
        this.startX = startX;
        this.startY = startY;
        this.width = width;
        this.height = height;
        this.type = type;
        this.color = color;
    }
}
```


### Karışık Örnekler

```java
JButton btn = new JButton("Kaydet");
btn.setActionCommand("save_db");
// ... ActionListener eklendiğini varsay ...

public void actionPerformed(ActionEvent e) {
    String buttonString = e.getActionCommand();
}
//output: normalde buttonString "kyadet" dönerdi ancak setActionCommand ile düzenlenmiş
```

`
setDefaultCloseOperation satırını tamamen silseydik ve sağ üstteki "X" butonuna bassaydık ne olurdu?

Cevap: default ayarda java'da kapatma kısmı HIDE_ON_CLOSE ayarındadır.
`

```java
JTextArea alan = new JTextArea(20, 50);
JScroolPane kaydirici = new JScrollPane(alan);
kaydirici.setVerticalScrollBarPolicy(JScrollPane.VERTICAL_SCROLLBAR_ALWAYS);
kaydirici.setHorizontalScrollBarPolicy(JScrollPane.HORIZONTAL_SCROLLBAR_AS_NEEDED);
```


## Lambda Expressions

Anonymous metotların tanımlanması için kullanılan hızlı bi yöntem. `(parameter1, parameter2) -> {code block}` şeklinde tanımlanır.

### Örnek 1
```java
import java.util.Arrays;
import java.util.List;

interface StringFunction{   // bir interface içerisindeki fonksiyonun lambda olarak kullanılabilmesi için sadece ve sadece 1 adet fonksiyon bulunmalıdır. yoksa lambda ile neyin çalıştırılacağı bilinemez. türlerin aynı olması farklı olması fark etmiyor, int run(int n) falan olsa da yine de derlenmezdi. tek fonksiyon var, bu kadar.
    String run(String str);
}
public class Main{
    public static void main(String[] args) {
        StringFunction exclaim = (s) -> s + "!";
        StringFunction ask = (s) -> s + "?";
        printFormatted("Hello", exclaim);
        printFormatted("Hello", ask);

        //----------------------------------------------------------//

        StringFunction emphasize = (s) -> "***" + (s) + "***";
        StringFunction capitalize = (s) -> s.isEmpty() ? s : s.substring(0,1).toUpperCase() + s.substring(1).toLowerCase();
        StringFunction removeVowels = (s) -> s.replaceAll("[aeiouAEIOU]", "");

        // zincirlemek de mümkün
        List<StringFunction> functionChain = Arrays.asList(removeVowels, capitalize, emphasize);
        StringFunction chainedFunction = chainFunctions(functionChain);
        printFormatted("hello world", chainedFunction);


        // thread kullanımını da kolaylaştırır
        Runnable r = () -> System.out.println("hello from lambda");
        Thread t1 = new Thread(r);

        Thread t2 = new Thread(() -> System.out.println("Hello from t2"));
        t1.start();
        t2.start();
    }
    public static void printFormatted(String s, StringFunction function){
        String result = function.run(s);  // interface içerisinde yalnızca bir adet fonksyion olduğundan run bizmi yukarıda tanımladığımız exclaim ve ask fonksyionlarını otomatik olarak çalıştırır
        System.out.println(result);
    }
    public static StringFunction chainFunctions(List<StringFunction> chain){
        return (s) -> {
            String result = s;
            for(StringFunction sf : chain){
                result = sf.run(result);
            }
            return result;
        };
    }
}
```

### Karışık Örnekler

```java
interface Hesaplayici {
    int işlem(int a, int b);
    default void mesaj(String s) { System.out.println(s); }
}
//output: hata atmaz çünkü yukarıda yalnızca bir abstract metot olabilirr demiştik burada işlem lambda olarak kullanılablilir çünkü diğer metodun body'si mevcuttur.
```

```java
interface KareAl {
    int hesap(int n);
}

// Lambda satırı:
KareAl k = ________________________________ ;

//output: KareAl k = (n) -> n*n;
```

```java
StringFunction sf = (s) -> { 
    s.toUpperCase() 
}; // HATA VAR!

// hata çözdüldü: süslü parantez açılmış ise sadece  ; eklenerek problem çözülmez, return edilmesi gerekir içerideki değerin. 
// StringFunction sf = (s) -> {return s.toUpperCase(); }; şeklinde.
```

```java
int sayi = 10;
StringFunction sf = (s) -> s + sayi; 
// sayi = 20; // <-- Bunu yaparsan yukarıdaki Lambda satırı KIZAR (Hata verir).
// lambda bir nesne gibi davranır, sayi kullanılacağı zaman o anki değerinin bi capture'ını alır. aynı zamanda thread kullanımlarında da uyumsuzluk oluşturabilir.  
```

`
A) Runnable r = new Runnable() { public void run() { ... } };

B) Runnable r = () -> { ... };

farkları arasındaki cevap: Cevap: Anonymous Inner Class (A) her yazıldığında arka planda yeni bir .class dosyası oluşturur ve her çağrıldığında yeni bir nesne (object) yaratır. Belleği (heap) yorar.
Lambda (B) ise Java 7 ile gelen invokedynamic adlı bir mekanizmayı kullanır. Arka planda yeni bir sınıf dosyası oluşturmaz, sadece bir metot çağrısı gibi davranır. Yani hem diskte yer kaplamaz hem de RAM'de nesne kirliliği yapmaz.
`

```java
// Lambda yazarken parametrelerin tipini (String, int vs.) yazmak zorunda değiliz, Java bunu "tahmin" eder. Soru: Aşağıdaki tanımlardan hangisi HATALIDIR?

/**
 * 
 * 1) (String s) -> ... : Doğru. Tipini açıkça belirttin, parantez şart.
 * 2) s -> ... : DOĞRU. Eğer tek bir parametre varsa, ne paranteze ne de tip yazmaya gerek vardır. Java StringFunction'dan onun String olduğunu anlar.
 * 3) (s) -> ... : Doğru. Tip yazmadın ama parantez içine aldın, okeydir.
 * 4) String s -> ... : YANLIŞ. Eğer tip (String) yazıyorsan, onu mutlaka parantez içine almalısın. (String s) -> olmalıydı.
*/
```

```java
// return (s) -> {}; mevzusu için açıklama:
/*
Hacım, senin notundaki chainFunctions metodunda gördüğün o olay şudur: Bir metot, geriye çalıştırılmaya hazır bir "paket" (fonksiyon) döndürüyor.

Normalde bir metot int döner, String döner... Ama Java'da Lambda sayesinde bir metot, başka bir metodun "tarifini" de dönebilir.

Basit Örnek:
Düşün ki bir fabrikan var, bu fabrika sana "selamlama yapan bir robot" üretip veriyor.


*/
public static StringFunction selamlayiciUret(String ek) {
    return (s) -> s + " " + ek; // Burada hemen çalıştırmıyor, "şöyle çalışacak bir fonksiyon" üretip döndürüyor.
}
/*
Sen bunu çağırdığında:
StringFunction f = selamlayiciUret("Merhaba");
Artık f değişkeninin içinde (s) -> s + " Merhaba" kodu yüklü. Sen ne zaman f.run("Hakan") dersen o zaman çalışır.

Notundaki Zincirleme (Chain) Mantığı:
Sen listeyi veriyorsun, o metot sana: "Önce birinciyi, sonra ikinciyi, sonra üçüncüyü çalıştıran TEK BİR DEV FONKSİYON" hazırlayıp veriyor. Sen o dönen şeyi bir kez çağırıyorsun, o sırayla hepsini içeride hallediyor.
*/
```

```java
interface Sayable{
    String say();
}
interface Drawable{
    void draw();    //there has to be one function if we want to use this interface with lambda expression
//    void draw2(); we cannot create any other function, "() -> print" expression gets its head spins like hell and mentions that tf am i going to run?!?!?
}

//main içerisinde:
int width = 10;
Drawable drawObj = () -> System.out.println("Width: " + width);
drawObj.draw();

System.out.println("-------------");
Error (Syntax Error) - "Missing return statement"

Sayable sObj = () -> {return "I have nothing to say.";}; 


// şeklinde **`return` keyword'ü olmadan** yazılırsa:
// Sayable sObj = () -> {"I have nothing to say.";};  // ❌ HATA
```
**Sebebi:** Süslü parantez açmışsanız, içerisinde mutlaka `return` ifadesi olmalıdır. Yoksa Java derleyici "Bu kod bir şey döndürüyor mü?" diye sorduğunda "Hayır, sadece statement var" diye cevap alıp compile error atar.

**Çözümler:**

1. **Return'ü ekle (Sizin yaptığınız):**
```java
Sayable sObj = () -> {return "I have nothing to say.";};  // ✅ DOĞRU
```

2. **Süslü parantezi kaldır (Daha temiz):**
```java
Sayable sObj = () -> "I have nothing to say.";  // ✅ DOĞRU (implicit return)
```

Lambda'da süslü parantez kaldırıldığında Java otomatik olarak `return` yapar. Ama süslü parantez varsa, her şeyi açıkça yazmalısınız.

**Kural:** Single-line lambda → parantez kaldır. Multi-line lambda → parantez kalsın ama return'ü ekle.


```java
public static StringFunction boslukEkle(int miktar) {
    String bosluk = " ".repeat(miktar); // miktar kadar boşluk hazır
    
    return (______) -> ______ + bosluk; 
}

// Kullanımı:
StringFunction f = boslukEkle(5);
System.out.println(f.run("Selam") + "Naber"); // Çıktı: "Selam     Naber"

//cevap: (s) -> s + bosluk
```

```java
StringFunction f1 = (s) -> s + "1";
StringFunction f2 = (s) -> f1.run(s) + "2"; 
StringFunction f3 = (s) -> f2.run(s) + "3";

System.out.println(f3.run("Sayı:"));
//output: bu şekilde çalışabilmektedir. zincirleme muhabbeti burda da geçerli.
```

```java
int[] sayac = {0}; 
StringFunction f = (s) -> {
    sayac[0]++; // Dizinin içindeki elemanı değiştiriyoruz
    return s + sayac[0];
};
System.out.println(f.run("A"));
System.out.println(f.run("B"));

//output: hata atmaz, A1 ve B2 basar. normalde dışardaki değişkent değiştirilemez muhabbeti vardı ancak buraıs istisna. int[]sayac = {0} dendiğinde sayac burada bir pointerdır. lambda'nın içine giren ise onun adresidir dolayısı ile adres ve içeriği ile oynar. hala "effectively final" denir. 
```

```java
String s = "DIŞARI";
StringFunction f = (s) -> s + " İÇERİ"; // Buradaki (s) parametresi, dışarıdaki s ile aynı isimde.

//output: "Variable 's' is already defined" diyerek derleme hatası verir.
```



## Thread

Slaytta verilen ilk daire çizdirmeli örnekte patlamasının sebebi actionPerformed'ın programı kitlemesi sebebidir. Thread.sleep ile kitleyerek başka fonksiyonları devre dışı bırakır. Concurrent programming ile parallel development yapılıp tasklar dağıtılabilir. Çoklu thread'ler ile execute edilir. actionPerformed kendiliğinden bir thread oluşturur circle çizebilmek için. Bu yüzden de problem oluşur. Çözüm için 2. örneğe bak.

Java'da thread'ler iki yoldan teki ile yapılabilir. Ya Thread sınıfı extend edilir ya da Runnable interface'i implement edilir. İkisi birlikte kullanılamaz ikisinde de run() metodu var. `Runnable r = new Runnable(); Thread t = new Thread(r); t.start();` şeklinde thread içerisine Runnable verilerek de oluşturulabilir. Genelde implements Runnable tercih edilir.

Thread'i başlatabilmek için start() çağırılmalı, direkt olarak run() çağırılamaz. Start işlemi, yeni bir thread execution ederek run()'ı kendisi çağırır. Direkt olarak run() çağırıldığında execute edildiği satırdan aşağısını kitler thread'in bi anlamı kalmaz. Anlaşılacağı üzere de run() main thread içerisinde, start() ise yeni bir threat parçacığı ile çalışmaktadır.

Thread'i durdurabilmek için `stop` kullanmaktansa `t.interrupt()` şeklinde interrupt edilerek sonlandırılabilir. Aslında arkada dönen mevzu Thread data structure'ı içerisinde bir boolean flag'ı setlemek. `while(!Thread.interrupted())` şeklinde kontrolü sağlanabiliyor. `InterruptedException` ile yakalanır.

Normal ve Daemon thread'ler var. Daemon thread'ler genelde background servisler için kullanılır ve termine edilmez. Oluşturmak için setDaemon() çağırılır start()'tan önce. Daemon'lar yalnızca JVM tarafından terminate edilebilir. Diğer user thread'leri gibi değildir. Main'le birlikte sonlanır, JVM ise daemon'ları kapatıp kendini kapatır en son adımda.

Thread scheduler kısa kısa periyodlar ile thread'leri çalıştırır. Running time'lar değişkenlik gösterebilir tabii ki. Execute order için kesin bir şey söylenemez, scheduler kendisi ayarlar, ancak priority ayarlanabilir tabii ki. Yardımcı bazı metotlar:
```java
getId(): // Returns the ID of the thread.
getName(): // Returns the name of the thread.
setName(String name): // Changes the name of the thread.
getPriority(): // Returns the priority of the thread.
setPriority(int newPriority/*Thread.MAX_PRIORITY*/): // Changes the priority of the thread.
isDaemon(): // Checks if the thread is a daemon thread.
setDaemon(boolean on): // Changes the daemon status of the thread.
isAlive(): // Checks if the thread is alive.
join(): // Causes the current thread to wait until the thread it's called on has finished executing.
interrupt(): // Interrupts the thread.
isInterrupted(): // Checks if the thread has been interrupted.
```

### Örnek 1
```java
public class Main extends Thread{

    public static void main(String[] args) {
        Thread thread = new Thread(){
            public void run(){
                for(int i=0; i<5; i++)
                    System.out.println("T1: " + i);
            }
        };
        Thread thread2 = new Thread(new Runnable(){
            public void run(){
                for(int i=0; i<5; i++)
                    System.out.println("T2: " + i);
            }
        });
        // Lambdalı versiyon (Daha modern):
        Thread thread3 = new Thread(() -> {
            for(int i=0; i<5; i++) 
                System.out.println("T3: " + i);
        });
        thread.start();
        thread2.start();
        thread3.start();
        for(int i=0; i<5; i++)
            System.out.println("N: " + i);
    }   
}
/*
çıktı aşağıdaki gibi randomized gelebilir:
T1: 0
T1: 1
T1: 2
T1: 3
T1: 4
T3: 0
T3: 1
T3: 2
T3: 3
T3: 4
N: 0
T2: 0
T2: 1
T2: 2
N: 1
N: 2
T2: 3
N: 3
T2: 4
N: 4
*/
```

### Örnek 2
```java
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class FillDemo extends JFrame implements ActionListener {

    private class Packer extends Thread{
        public void run(){
            Graphics g = box.getGraphics(); // sıkıntısal, pencere minimize edilip açıldığında paint() baştan çalışır ve temzizlik yapar öncekileri siler. List olarak veriyi tutup paintComponent ile çizdirmek en mantıklısı.

            for (int y = 0; y < FILL_HEIGHT; y = y + CIRCLE_SIZE) {
                for (int x = 0; x < FILL_WIDTH; x = x + CIRCLE_SIZE) {
                    g.fillOval(x, y, CIRCLE_SIZE, CIRCLE_SIZE);
                    doNothing(PAUSE);
                }
            }
        }
    }
    
    public static final int WIDTH = 300;
    public static final int HEIGHT = 200;
    public static final int FILL_WIDTH = 300;
    public static final int FILL_HEIGHT = 100;
    public static final int CIRCLE_SIZE = 10;
    public static final int PAUSE = 100; //milliseconds

    private JPanel box;

    public static void main(String[] args) {
        FillDemo gui = new FillDemo();
        gui.setVisible(true);
    }

    public FillDemo() {
        setSize(WIDTH, HEIGHT);
        setTitle("FillDemo");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);

        setLayout(new BorderLayout());
        box = new JPanel();
        add(box, BorderLayout.CENTER); 

        JPanel buttonPanel = new JPanel();
        buttonPanel.setLayout(new FlowLayout());
        JButton startButton = new JButton("Start");
        startButton.addActionListener(this);
        buttonPanel.add(startButton);
        add(buttonPanel, BorderLayout.SOUTH); 
    }

    public void actionPerformed(ActionEvent e) {
        //fill();   -> HATA ATAR, yukarıda da bahsedilen kilit olayına sokar o yüzden yeni oluşturduğumuz Packer sınıfına bırakacağız execute mevzusunu.
        Packer threadPacker = new Packer();
        threadPacker.start();   // artık programın geri kalanı kitlenmeden istediğimiz işleri de halledebileceğiz.
    }

    // ucube fonksiyon
    public void fill() {
        Graphics g = box.getGraphics();

        for (int y = 0; y < FILL_HEIGHT; y = y + CIRCLE_SIZE) {
            for (int x = 0; x < FILL_WIDTH; x = x + CIRCLE_SIZE) {
                g.fillOval(x, y, CIRCLE_SIZE, CIRCLE_SIZE);
                doNothing(PAUSE);
            }
        }
    }

    public void doNothing(int milliseconds) {
        try {
            Thread.sleep(milliseconds);
        } catch(InterruptedException e) {
            System.out.println("Unexpected interrupt");
            System.exit(0);
        }
    }
}
```


Data Race, iki farklı thread'in aynı veri üzerinde yazma okuma işlemleri yaparak veri tutarlılığının bozulması. Senkron bir yapı kurularak çözülmelidir. Özellikle shared resource'lar mevcutsa senkron yapı kesinlikle gereklidir. 

`synchronized` keyword'ü ile bu consistency problemi çözülebilir. `public synchronized void deposit(int amount){}` ile oluşturularak bu method çağırıldığında atomic olarak çalışır, yalnızca bir thread bu fonksiyonu kullanabilir hale gelir birden fazla thread'in aynı anda kendisini kullanmasına izin vermez. Race condition engellenmiş olur. Fonksiyon senkron tanımlanmıyor ise içeride bloğu tanımlanabilir. `blabla{synchronized(this) { /*critical session*/}}`

### Örnek 3
```java
public class Reentrant{
    public synchronized outer(){
        inner();
    }
    public synchronized inner(){
        // do smth
    }
}
/*
Deadlock?"

Cevap: HAYIR, hiçbir sorun çıkmaz! Bu kod paşalar gibi çalışır. Çözüm: Reentrant Locking (Yeniden Girebilir Kilitleme).


Java'da synchronized anahtar kelimesi, nesne üzerindeki bir kilidi (monitor) ele geçirmek demektir.

Thread outer() metoduna girerken kapıyı kilitler ve anahtarı cebine koyar.

outer()'ın içindeyken birden inner() metodunu çağırması gerekir.

inner() da kilitli (synchronized) bir odadır. Java bakar: "Hop, bu odaya girmeye çalışan kim? Aaa bizim outer'ı tutan eleman! E anahtar zaten adamın cebinde, niye kapıda bekletiyoruz ki?" der ve içeri buyur eder.

İşte buna Reentrancy denir. Eğer bu özellik olmasaydı, bir thread kendi tuttuğu kilidi açamadığı için kendi kendini kilitlerdi (Self-Deadlock).


Hoca burada senin şu hataya düşmeni bekliyor:
"Bir thread bir kilidi tutarken, aynı kilidi isteyen başka bir metodun kapısında kalır."

Doğru, başka bir thread olsa kapıda kalırdı. Ama aynı thread (ayni işçi) için Java "Sen zaten yetkilisin, geç içeri" diyor.


Java bunu arkada bir Counter (Sayaç) tutarak halleder:

Thread outer()'a girer -> Kilit sayacı: 1.

inner()'ı çağırır -> Kilit sayacı: 2. (Hala aynı thread)

inner() biter -> Kilit sayacı: 1.

outer() biter -> Kilit sayacı: 0. (Kilit tamamen serbest kalır, artık başkası girebilir).

Örneğin devamı:
*/
class Baba {
    public synchronized void harclikVer() {
        System.out.println("Baba: Al evladım 100 TL, ama düzgün harca.");
    }
}

class Ogul extends Baba {
    @Override
    public synchronized void harclikVer() {
        System.out.println("Oğul: Baba valla çok lazım, bir 100 daha ateşle.");
        
        super.harclikVer(); 
        
        System.out.println("Oğul: Toplam 200 kaptık, akşama pavyon!");
    }
}

public class ReentrancyTest {
    public static void main(String[] args) {
        Ogul kerem = new Ogul();
        
        new Thread(() -> {
            kerem.harclikVer();
        }).start();
    }
}
// Altın Kural: Java'da kilitler 'Thread' bazlıdır, 'Metot' bazlı değil. Bir thread bir objenin tapusunu (lock) bir kere aldıysa, o objenin içindeki bütün kilitli odalara elini kolunu sallayarak girer.
```

### Örnek 4 - Stack
```java
public class Stack{
    private int arr[];
    private int top;
    private int capacity;
    private Object lock1;
    private Object lock2;

    Stack(int capacity){
        arr[] = new int[capacity];
        this.capacity = capacity;
        top = -1;
        lock1 = new Object();
        lock2 = new Object();
    }
    public synchronized void push(int i){
        if(!isFull())
            arr[++top] = i;
    }
    public void push2(int i){
        synchronized(this){
            if(!isFull())
                arr[++top] = i;
        }
    }
    public void push3(int i){
        synchronized(lock1){ // önceden tüm metotlar aynı thread tarafından kullanılabilirken ancak burada diyoruz ki arr kullanılacağı zaman lock kullanılsın ve lock'u bilebilenler kullansın o yüzden private tanımlayıp burada capsüle ettik
            if(!isFull())
                arr[++top] = i;
        }
    }
    public synchronized int pop(){
        if(!isEmpty())
            return arr[top--];
    }
    public  int pop2(){
        synchronized(this){
            if(!isEmpty())
                return arr[top--];
        }
    }
    public  int pop2(){
        synchronized(lock2){    // burası push3'ten farklı olarak lock2 ile kitlemekte, push için olanlar lock1 kuyruğuna pop için gelenler lock2 kuyruğunda beklesin diyo teorik olarak güzel gibi gelmekte ama pratikte korkunç. Dümdüz race condition'a sebep olur. İki farklı lock aynı nesne üzerinde oynama YAPILMIYOR ise kullanılabilir.
            if(!isEmpty())
                return arr[top--];
        }
    }
}
```

Static locklarda normal lock'ların aksine obje değil(myStack) sınıf kitlenir(Stack.class). Paralel çalışabilirler çünkü az önce yazıldığı üzere birbirinden farklı sistemleri kitlemektedirler. Static genelde normalde de olduğu gibi bütün objelerin ortaklaşa kullandığı(mesela `static int count`) değişkenler için kullanılabilir. Mutlaka `static synchronized` ile korunmalı, race condition engellenmeli.

### Örnek 5
```java
public class BankaSubesi {
    private static double merkezBankaBakiyesi = 1000000.0;
    
    private double musteriBakiyesi = 0.0;
    
    private final Object kiralikKasaKilidi = new Object();

    // --- A. STATIC SYNCHRONIZED (Bütün Şubeleri Durdurur) ---
    public static synchronized void merkezdenParaCek(double miktar) {
        System.out.println(Thread.currentThread().getName() + " -> Genel Merkezden para çekiliyor...");
        merkezBankaBakiyesi -= miktar;
        // Bu işlem bitene kadar DÜNYADAKİ HİÇBİR ŞUBE merkezden para çekemez!
        try { Thread.sleep(3000); } catch (InterruptedException e) {}
    }

    // --- B. SYNCHRONIZED (THIS) (Sadece O Müşteriyi Durdurur) ---
    public synchronized void hesabimaParaYatir(double miktar) {
        System.out.println(Thread.currentThread().getName() + " -> Müşteri hesabına yatırıyor...");
        this.musteriBakiyesi += miktar;
        // Bu işlem bitene kadar bu müşterinin hesabına başka kimse dokunamaz.
        try { Thread.sleep(3000); } catch (InterruptedException e) {}
    }

    // --- C. SYNCHRONIZED (LOCK) (Sadece Kiralık Kasayı Durdurur) ---
    public void kiralikKasayiAc() {
        synchronized (kiralikKasaKilidi) {
            System.out.println(Thread.currentThread().getName() + " -> Kiralık kasa inceleniyor...");
            // Müşteri parasını yatırırken (B) aynı anda kasasını da açabilir! 
            // Çünkü kilitler farklı!
            try { Thread.sleep(3000); } catch (InterruptedException e) {}
        }
    }
}
```

Java'daki Lock objeleri senkronizasyon işinin daha manuel yapılan hali, kontrol bizde daha optimal ancak daha riskli tabii ki. Önceki keywordler basit işler için yeterli ancak daha optimal çözümler için locks paketi kullanılır. İçerisinde **ReentrantLock** ve **ReadWriteLock** bulunur.

- ReentrantLock: En popüler olanı. Bir thread zaten sahip olduğu kilidi tekrar alabilir (hatırlarsan babanın odası örneğindeki mevzu).
- ReadWriteLock: Okuma yapanlar birbirini beklemesin ama yazma yapan herkesi durdursun diyen daha kompleks bir yapı.

Lock objelerinin kullanım metotları:

- lock(): Kapıyı kilitler. Eğer anahtar başkasındaysa, thread orada uyur (disabled olur) ve anahtar boşa çıkana kadar bekleme listesinde kalır.
- unlock(): Kapıyı açar. Anahtarı bırakır ki sıradaki thread içeri girebilsin. Bunu çağırmayı unutursan programın sonsuza kadar kilitlenir (Deadlock).
- tryLock(): Sıkıntılı olan metot bu. Kapıyı zorlamaz. "Anahtar boşta mı?" diye bakar. Boştaysa alır (true), değilse beklemek yerine hemen vazgeçer (false). Thread'in kapıda boş boş beklemesini engeller.

Bir `class BankAccount` içerisinde `Lock balanceChangeLock = new ReentrantLock();` diyerek para ile oynayan birden fazla thread varsa bunları bu Lock ile kontrol altına alırız. `balance` değişkenine dokunmadan hemen önce `lock()` deriz, işimiz bitince de `unlock()` diyerek diğer thread'lere izin veririz.

**Önemli:** locked vaziyette iken exception atılırsa unlock hiç çalışmayacağı için program kitlenir. Dolayısı ile finally bloğu içerisinde elbet unlock edilmelidir.

### Örnek 6
```java
public class BankAccount{
    private double balance;
    private Lock balanceChangeLock;
    
    BankAccount(){
        balanceChangeLock = new ReentrantLock();
        balance = 0;
    }
    public void deposit(double amount){
        balanceChangeLock.lock();

        try{
            balance += amount;
        }
        catch(Exception e){
            e.printStackTrace();            
        }
        finally{
            balanceChangeLock.unlock();
        }
    }
}
```

Thread'ler birbirleri aralarında join(), wait(), notify(), notifyAll() gibi metotlar ile haberleşebilir. wait() ile bekletilen thread, başka bir thread'in notify fonksiyonlardan birisini bekler ve o şekilde uyanır. join() metodu çalıştırldığında onu çalıştıran thread tek başına execute edilmeye başlanır. O sırada diğer tüm akış kontrolü devralan thread bitene kadar bekletilir. join()'de eğer çağırıldığı threat çalışmaktaysa o ölene kadar bekle. (Karışık örneklerdeki t1.start() ve t1.join() çağırılan örneğe bak.)

Producer ve Consumer threadleri olsun. 

Producer thread'i buffer'a eklenmesi için data üretir. Consumer eğer buffer'daki son mesajı okumadıysa bekler. Buffer'a yazdıktan sonra da consumer'ı notify eder.

Consumer thread'i ise buffer'dan veri okur, buffer boş ise wait çağırır.

Buffer ise paylaşımlı bir data alanı.

### Örnek 7 - Producer & Consumer
```java
interface Buffer{
    public void set(int value);
    public int get();
}
class UnsynchronizedBuffer implements Buffer{
    private int buffer = -1;

    public void set(int value){
        System.err.println(Thread.currentThread().getName() + " writes " + value);
        buffer = value;
    }
    public int get(){
        System.err.println(Thread.currentThread().getName() + " reads " + buffer);
        return buffer;
    }
}
class SynchronizedBuffer implements Buffer{
    private int buffer = -1;
    private int occupiedBufferCount = 0;    //  bu buffer tek hücrelik kapasitede, sürekli yazılırsa önceki veri kaybolcağından bu da onun kontrolünü yapıyo 0 ise buffer boş, 1 ise buffer dolu demektir. boşken consumer okumaya çalışırsa ve doluyken de producer de yazmaya çalışırsa wait() çağırılır.

    public synchronized void set(int value){
        String name = Thread.currentThread().getName();

        while(occupiedBufferCount == 1){
            try{
                System.err.println(name + " tries to write.");
                displayState("Buffer full. " + name + " waits.");
                wait();
            }
            catch(InterruptedException e){
                e.printStackTrace();
            }
        }
        
        buffer = value;
        ++occupiedBufferCount;
        displayState(name + " writes " + buffer);
        notify();
    }
    public synchronized int get(){
        String name = Thread.currentThread().getName();

        while(occupiedBufferCount == 0){
            try{
                System.err.println(name + " tries to read.");
                displayState("Buffer empty. " + name + " waits.");
                wait();
            }catch(InterruptedException e){
                e.printStackTrace();
            }
        }
        
        --occupiedBufferCount;

        displayState(name + " reads " + buffer);
        notify();

        return buffer;
    }
    public void displayState(String operation){
        StringBuffer outputLine= new StringBuffer( operation );
        outputLine.setLength( 40);
        outputLine.append( buffer + "\t\t"+ occupiedBufferCount);
        System.err.println( outputLine);
        System.err.println();
    }
}

class Producer extends Thread{
    private Buffer sharedLocation;

    Producer(Buffer shared){
        super("Producer"); //   name set etmekte.
        sharedLocation = shared;
    }

    public void run(){
        for(int i=1; i<=4; i++){
            try{
                Thread.sleep(1000); // amacı gerçekçi simülasyon ve race condition engellemek
                sharedLocation.set(i);
            } catch(InterruptedException e){
                e.printStackTrace();
            }
        }

        System.err.println(getName() + " done producting.\nTerminating: " + getName() + ".");
    }
}
class Consumer extends Thread{
    private Buffer sharedLocation;

    Consumer(Buffer shared){
        super("Consumer"); //   name set etmekte.
        sharedLocation = shared;
    }
    public void run(){
        int sum = 0;
        for(int i=1; i<=4; i++){
            try{
                Thread.sleep(1000); // TOOD: belirli bi amacı var mı?
                sum += sharedLocation.get();
            }catch(InterruptedException e){
                e.printStackTrace();
            }
        }

        System.err.println(getName() + " read values totailng: " + sum + ".\nTerminating: " + getName() + ".");
    }
}
public class SharedBufferTest{
    public static void main(String[] args){
        // TEST 1
        /* Buffer sharedLocation = new UnsynchronizedBuffer();

        Producer producer = new Producer(sharedLocation);
        Consumer consumer = new Consumer(sharedLocation);

        producer.start();
        consumer.start();
 */
        // TEST 2
        SynchronizedBuffer sharedLocation= new SynchronizedBuffer();
        // Display column heads for output
        StringBuffer columnHeads= new StringBuffer( "Operation");
        columnHeads.setLength( 40);
        columnHeads.append( "Buffer\t\tOccupiedCount");
        System.err.println( columnHeads);
        System.err.println();
        sharedLocation.displayState( "Initial State");
        // create producer and consumer objects
        Producer producer = new Producer( sharedLocation);
        Consumer consumer = new Consumer( sharedLocation);

        producer.start();
        consumer.start();
    }
}
```

### Karışık Örnekler

```java
public void withdraw(double amount) {
    if (balanceChangeLock.__________) { // Kilidi almayı dene, alamazsan bekleme
        try {
            balance -= amount;
        } finally {
            balanceChangeLock.__________;
        }
    } else {
        System.out.println("Kasa meşgul, sonra gel.");
    }
}
//cevap: tryLock() ve unlock()
```

```java
Thread t1 = new Thread(() -> {
    try { Thread.sleep(2000); } catch(Exception e){}
    System.out.print("Thread-Bitti ");
});

t1.start();
System.out.print("Main-1 ");
t1.join();
System.out.print("Main-2 ");

// output: Main-1 Thread-Bitti Main-2 olurdu çünkü akış şu şekilde. 
/*
1. t1 başlatılır ancak 2 saniye uyur
2. t1 uyurken Main-1 yazdırılır
3. Sonrasında gelen t1.join() ile t1'in işinin bitilmesi beklenir, t1 de Thread-Bitti printletir ve main'i tekrardan salar.
4. Sonraki son satırda da Main-2 bastırılır bu kadar.
*/
```

```java
BankaSubesi sube1 = new BankaSubesi();
BankaSubesi sube2 = new BankaSubesi();

// Thread 1:
sube1.hesabimaParaYatir(100); 

// Thread 2:
sube2.hesabimaParaYatir(200);

//output: sıkıntı olmaz çünkü farklı objeler farklı değişkenlerine erişim sağlanmakta. burada bir lock mekanizması aktif olarak görülmez. 
```

```java
public class Soru {
    public static void main(String[] args) throws InterruptedException {
        Thread tMain = Thread.currentThread(); // Ana thread'in referansını alıyoruz

        Thread t1 = new Thread(() -> {
            try {
                System.out.print("T1-Bekliyor ");
                tMain.join(); // T1, ana thread'in (main) bitmesini bekliyor!
                System.out.print("T1-Bitti ");
            } catch (Exception e) {}
        });

        t1.start();
        System.out.print("Main-Bekliyor ");
        t1.join(); // Main, T1'in bitmesini bekliyor!
        System.out.print("Main-Bitti ");
    }
}

//cevap: hiçbir zaman çalışma bittmez çünkü tMain ve t1 birbirlerini beklerken kitlemiş olurlar
```

```java
class Banka {
    public static synchronized void merkezIslem() {
        System.out.print("M-Başladı ");
        try { Thread.sleep(2000); } catch(Exception e){}
        System.out.print("M-Bitti ");
    }

    public synchronized void subeIslem() {
        System.out.print("S-Başladı ");
    }
}

// Main içinde:
Banka b1 = new Banka();
new Thread(() -> Banka.merkezIslem()).start(); // Thread-1
new Thread(() -> b1.subeIslem()).start();      // Thread-2
```

```java
Thread t1 = new Thread(() -> {
    try {
        System.out.print("Uyuyorum ");
        Thread.sleep(10000); // 10 saniye uyku
        System.out.print("Uyandım ");
    } catch (InterruptedException e) {
        System.out.print("Böldüler! ");
    }
});

t1.start();
Thread.sleep(1000); // Main 1 saniye bekleyip t1'i dürtüyor
t1.interrupt();

//output: uyuoyurm yazılır. interrupt edileince de exception catch edip böldüler print ettirilir.
```

```java
import java.util.concurrent.locks.*;

class AkıllıKasa {
    private String paket = null;
    private final Lock kilit = new ReentrantLock();
    private final Condition dolu = kilit.newCondition();
    private final Condition bos = kilit.newCondition();

    public void paketBırak(String p) throws InterruptedException {
        kilit.lock();
        try {
            while (paket != null) {
                // 1. BOŞLUK: Kasa doluysa kurye hangi condition'da beklemeli?
                ____________________; 
            }
            paket = p;
            System.out.print("Bırakıldı ");
            // 2. BOŞLUK: Müşteriyi (Consumer) uyandır
            ____________________; 
        } finally {
            kilit.unlock();
        }
    }

    public String paketAl() throws InterruptedException {
        kilit.lock();
        try {
            while (paket == null) {
                // 3. BOŞLUK: Kasa boşsa müşteri hangi condition'da beklemeli?
                ____________________; 
            }
            String p = paket;
            paket = null;
            System.out.print("Alındı ");
            // 4. BOŞLUK: Kuryeyi (Producer) uyandır
            ____________________; 
            return p;
        } finally {
            kilit.unlock();
        }
    }
}


// boşluklar sırası ile:  wait(), notify(), wait(), notify()
```

## Stream API

Fonksiyonel programlama ile computation block'ları aktarılabilir. Paralel işleme için thread'ler arası dağıtılabilir. Aşağıdaki kod bloğu en iyi örnektir:

```java
int sum = 0;
for(int num : numbers)
    if(num%2==0)
        sum += num;

// değil de, aşağıdaki

int sum = numbers.filter(i -> i % 2 == 0).sum();
```

Önceden anlatılan lambda expression'ların türleri:

- Standart form: `(int x, int y) -> {System.out.println("adding " +  x + " and " + y); return x+y;}`
- With inferred parameter types: `(x,y) -> {return x+y;}`
- Single expression with implicit return: `(x,y) -> x+y`
- Single parameter(parantheses optional): `value -> System.out.println("value: " + value);`
- Empty parameter list: `() -> System.out.println("hello")`

### Örnek 1
```java
// implementing runnable interface
Runnable r = () -> System.out.println("running in a seperate thread");

// sorting with comparator
List<String> names = Arrays.asList("kerem", "ali");
Collection.sort(names, (a,b) -> (a.length() - b.length()));

// basic functional interfaces
Predicate<String> isEmpty = s -> s.isEmpty();
Function<Integer, String> intToString = i -> "Number: " + i;
Consumer<String> print = s -> System.out.println(s);
```

Methods references ile lambda ifadeleri için kısaca notasyonlar bulunmaktadır.

- x -> x.instanceMethod() ==> ClassName::instanceMethodName
- x -> object.instanceMethod(x) ==> objectName::instanceMethodName
- x -> ClassName.staticMethod(x) ==> ClassName::staticMethod
- () -> new ClassName() ==> ClassName::new

### Örnek 2
```java
//ClassName::instanceMethodName
UnaryOperator<String> upper = String::toUpperCase;
String name = upper.apply("kerem");

//objectName::instanceMethodName
String sb = new String();
Consumer<String> appender = sb.concat; // instead of s -> sb.concat(s)
appender.accept("Hello");

//ClassName::staticMethod
Function<String, Integer> parser = Integer::parseInt;
int value = parser.apply("42");

//ClassName::new
Supplier<ArrayList<String>> listCreator = ArrayList::new;
ArrayList<String> newList = listCreator.get();
```

Stream interface'i ile stream objeleri implement edilebilir. Bir element  koleksiyonuna fonksiyonel eklemeler yapabilir. Pipeline'ı aşağıdaki gibi kurulabilir:

### Örnek 3

```java
List<String> myList = Arrays.asList("kerem", "ali");

long count = myList.stream()
            .filter(s -> s.startsWith("a")
            .map(String::upperCase)
            .count());

// stream'de data tutulmaz işlendi mi tekrar kullanılamaz, lazy çalışır sırası ile ilerler. bazı diğer fonksiyonları:
/*
filter -> stream().filter(n -> n>5)
map -> stream().map(String::length)
distinct -> stream().distinct() -> unique elementler
limit/skip -> stream().limit(3) -> 3 adet elemanın limitlenmesi veya skiplenmesi için
sorted -> stream().sorted() -> adı üstünde
*/

List<String> myList = Arrays.asList("kerem", "ali");

List<String> result = myList.stream()
                    .filter(s -> s.length() > 3)
                    .distinct()
                    .map(String::toUpperCase)
                    .collect(Collectors.toList());

// forEach -> stream().forEach(fruit -> println(fruit)) -> tek tek fruit list, array neyse artık gezerek işler                    

myList.stream().forEach(name -> System.out.println("Name: " + name));


// reduction metotları
/*
average -> stream().mapToInt(x -> x).average()
count -> stream().count()
max -> stream().max(Comparator.naturalOrder())
min -> stream().min(Comparator.naturalOrder())
reduce -> stream().reduce(0, (a,b) -> (a+b))
*/

List<Integer> nums = Arrays.asList(1,2,3,4,5,6,7,8,9);

long count = numbers.stream().count();
int sum = numbers.stream().reduce(0, Integer::sum);
double avg = numbers.stream().mapToInt(i -> i).average().orElse(0);
int max = numbers.stream().max(Integer::compare).orElse(0);
int min = numbers.stream().min(Integer::compare).orElse(0);

// collect metotları
/*
collect()
toArray()
*/

List<String> words = Arrays.asList("kerem", "ali");

List<String> filtered = words.stream()
                            .filter(s -> s.length() > 4)
                            .collect(Collectors.toList());

Set<String> uniqueFirstLetters = words.stream()
                                    .map(s -> s.substring(0,1))
                                    //.distinct() opsiyonel set zaten bunu yapıyo kendi
                                    .collect(Collectors.toSet());

String[] array = words.stream()
                    .toArray(String[]::new);

// search metotları
/*
findFirst
findAny
anyMatch
allMatch
*/

List<String> names = Arrays.asList("kerem", "ali");

Optional<String> firstNameWithK = names.stream()
                                    .filter(name -> name.startsWith("K"))
                                    .findFirst();

boolean hasLength3 = names.stream()
                        .anyMatch(name -> name.length()==3);

boolean allAtLeast3Chars = names.stream()
                            .allMatch(name -> name.length() >= 3);
```

IntStream, LongStream gibi numerik türden özelleştirilmiş operasyonlar bulunmakta. IntStream integer değerleri için spesifik olan numerik operator stream'i. Matruşka gibi olan integer işlemlerini kullanmaktan daha efektif.

### Örnek 4

```java
IntStream fromArray = IntStream.of(1,2,3,4,5);
IntStream range= IntStream.range(1,6); // 1 to 5
IntStream rangeClosed = IntStream.rangeCloed(1,6); // 1 to 6

int sum = IntStream.rangeClosed(1,100).sum();
double avg = IntStream.rangeClosed(1,100).average();
int max = IntStream.rangeClosed(1,100).max().getAsInt();
int min = IntStream.rangeClosed(1,100).min().getAsInt();
long count = IntStream.rangeClosed(1,100).count();

IntStream numbers = IntStream.rangeClosed(1,100);
IntSummaryStatistics stats = numbers.summaryStatistics();

System.out.println(stats.getCount());
System.out.println(stats.getSum());
System.out.println(stats.getMax());
System.out.println(stats.getMin());
System.out.println(stats.getAverage());
```

### Örnek 5

```java
Integer[] values = { 2, 9, 5, 0, 3, 7, 1, 4, 8, 6 };

// Create stream from array
List<Integer> sortedNumbers = Arrays.stream(values)
        .sorted() // Sort in natural order [cite: 909]
        .collect(Collectors.toList()); // [cite: 910]
// Result: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9] [cite: 911]

// Filter even numbers and collect
List<Integer> evenNumbers = Arrays.stream(values)
        .filter(n -> n % 2 == 0) // [cite: 914]
        .collect(Collectors.toList()); // [cite: 915]
// Result: [2, 0, 4, 8, 6] [cite: 916]
```


### Örnek 6

```java
Integer[] values = { 2, 9, 5, 0, 3, 7, 1, 4, 8, 6 };

// Predicates
Predicate<Integer> greaterThan5 = n -> n > 5; // [cite: 938]
Predicate<Integer> evenNumbers = n -> n % 2 == 0; // [cite: 938]
Predicate<Integer> lessThan7 = n -> n < 7; // [cite: 938]

// Even numbers less than 7
List<Integer> result = Arrays.stream(values)
        .filter(evenNumbers.and(lessThan7)) // [cite: 940]
        .collect(Collectors.toList());
// Result: [2, 0, 4, 6] [cite: 941]

// Numbers that are either even or greater than 5
List<Integer> result2 = Arrays.stream(values)
        .filter(evenNumbers.or(greaterThan5)) // [cite: 943]
        .collect(Collectors.toList());
// Result: [2, 9, 0, 7, 4, 8, 6] [cite: 944]
```

### Örnek 7
```java
List<String> words = Arrays.asList("Apple", "banana", "Cherry", "date", "Blueberry");

// Convert all strings to uppercase using map and method reference
List<String> upperCase = words.stream()
        .map(String::toUpperCase) // [cite: 964]
        .collect(Collectors.toList()); // [cite: 965]
// Result: [APPLE, BANANA, CHERRY, DATE, BLUEBERRY] [cite: 966]

// Sort strings naturally (case-sensitive)
List<String> naturalSorted = words.stream()
        .sorted() // [cite: 969]
        .collect(Collectors.toList());
// Result: [Apple, Blueberry, Cherry, banana, date] (uppercase before lowercase) [cite: 971]

// Sort strings case-insensitively
List<String> caseInsensitiveSorted = words.stream()
        .sorted(String.CASE_INSENSITIVE_ORDER) // [cite: 974]
        .collect(Collectors.toList()); // [cite: 975]
// Result: [Apple, banana, Blueberry, Cherry, date] [cite: 976]
```