---
layout: tutorial
title: Lokalno zaključivanje tipova (type inference)

disqus: true

tutorial: scala-tour
categories: tour
num: 28
outof: 33
language: ba
---
Scala ima ugrađen mehanizam zaključivanja tipova koji dozvoljava programeru da izostavi određene anotacije tipova.
Često nije potrebno specificirati tip varijable u Scali,
jer kompajler može sam zaključiti tip iz inicijalizacijskog izraza varijable.
Povratni tipovi metoda također mogu biti izostavljeni jer oni odgovaraju tipu tijela (zadnji izraz u tijelu), koje kompajler sam zaključi.

Slijedi jedan primjer:

    object InferenceTest1 extends App {
      val x = 1 + 2 * 3         // tip x-a je Int
      val y = x.toString()      // tip y-a je String
      def succ(x: Int) = x + 1  // metoda succ vraća Int
    }

Za rekurzivne metode, kompajler nije u mogućnosti da zaključi tip rezultata.
Ovo je program koji se ne može kompajlirati iz ovog razloga:

    object InferenceTest2 {
      def fac(n: Int) = if (n == 0) 1 else n * fac(n - 1)
    }

Također nije obavezno specificirati tipske parametre kada se pozivaju [polimorfne metode](polymorphic-methods.html) 
ili kada se [generičke klase](generic-classes.html) instanciraju.
Scala kompajler će zaključiti nedostajuće tipske parametre iz konteksta i iz tipova stvarnih parametara metoda/konstruktora.

Ovo je primjer koji to ilustrira:

    case class MyPair[A, B](x: A, y: B);
    object InferenceTest3 extends App {
      def id[T](x: T) = x
      val p = MyPair(1, "scala") // tip: MyPair[Int, String]
      val q = id(1)              // tip: Int
    }

Zadnje dvije linije ovog programa su ekvivalentne sljedećem kodu gdje su svi zaključeni tipovi eksplicitno napisani:

    val x: MyPair[Int, String] = MyPair[Int, String](1, "scala")
    val y: Int = id[Int](1)

U nekim situacijama može biti vrlo opasno osloniti se na Scalin mehanizam zaključivanja tipova:

    object InferenceTest4 {
      var obj = null
      obj = new Object()
    }

Ovaj program se ne može kompajlirati jer je zaključeni tip varijable `obj` tip `Null`.
Pošto je jedina vrijednost tog tipa `null`, nemoguće je dodijeliti ovoj varijabli neku drugu vrijednost.
