---
published: true
title: Wywoływanie wirtualnych metod z konstruktora
layout: post
tags: [c#]
---
Jak wiadomo klasy w języku C# mogą po sobie dziedziczyć. Proces dziedziczenia pozwala na tworzenie nowych klas, które mogą rozszerzać i modyfikować zachowanie innych klas. Zakładając, że klasa A posiada metodę X, a klasa B dziedziczy po klasie A, to w klasie B można używać implementacji metody z klasy A. Poprzez rozszerzalność można w tym przypadku rozumieć, że dopisujemy pewne specyficzne dla klasy B metody, których klasa A nie potrzebuje. W przypadku modyfikacji, można całkowicie zmienić zachowanie dziedziczonej metody X z klasy A. Aby było to możliwe należy skorzystać z dwóch słów kluczowych przy deklaracji metod. W klasie bazowej trzeba wykorzystać słowo **virtual**, natomiast w klas dziedziczącej słowo **override**. Dodać warto na koniec, że konstruktory klas nie podlegają procesowi dziedziczenia. Gdy tworzona zostanie instancja klasy dziedziczącej, to w pierwszej kolejności wywoła się konstruktor klasy bazowej. W związku z kolejnością wywoływań konstruktorów i wywołań w nich metod oznaczonych jak virtual wiąże się pewne niebezpieczeństwo.

{% highlight csharp %}
public class BaseClass
{
    public BaseClass()
    {
        DoSomething();
        Console.WriteLine(Text.Length);
    }

    public string Text { get; set; }

    public void DoSomething()
    {
        Text = "lorem ipsum";
    }
}
{% endhighlight %}

Przedstawiona klasa posiada wywołanie w konstruktorze metody *DoSomething*, która inicjalizuje wartość właściwości *Text*, a następnie po wyjściu z tej metody wyświetlana jest na ekranie liczba znaków właściwości *Text*. Przechodzą do wspominanej groźnej sytuacji niech metoda *DoSomething* będzie oznaczona jako **virtual**.

{% highlight csharp %}
public class DerivedClass : BaseClass
{
    public override void DoSomething()
    {
            
    }
}
{% endhighlight %}

Prosta klasa, która dziedziczy po klasie *BaseClass* i nadpisuje metodę *DoSomething*, chociażby w taki sposób, że jest pusta. Przy tworzeniu obiektu klasy *DerivedClass* zaistnieje następująca sytuacja. W pierwszej kolejności zostanie wywołany konstruktor klasy bazowej, w którym wywoływana jest metoda *DoSomething*. Jednakże w klasie dziedziczącej jest ona nadpisana, więc nie odbędzie się przypisanie pewnej wartości do właściwości *Text* jak w poprzedniej sytuacji. Po opuszczeniu metody, będzie wykonywana dalsza cześć konstruktora, w której na ekran wyświetlana jest właściwość *Length* i w tym momencie ukaże się wyjątek **System.NullReferenceException** ponieważ domyślną wartość typu string jest null i dostęp do właściwości *Length* jest niemożliwy.

Warto zauważyć, że *Resharper* przed taką czynnością próbuje ostrzec komunikatem, że istnieje próba wywołania metody wirtualnej z konstruktora klasy i zaleca uczynić daną klasę **sealed**, co z kolei zapobiegło by możliwości dziedziczenia po takiej klasie.