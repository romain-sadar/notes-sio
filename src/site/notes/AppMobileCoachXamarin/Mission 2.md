---
{"dg-publish":true,"permalink":"/app-mobile-coach-xamarin/mission-2/","dgPassFrontmatter":true}
---

# Persistance des données grâce à la serialisation

## Principe 

La sérialisation peut être définie comme le processus de stockage de l'état d'un objet sur un support. ​

Pendant ce processus, les champs publics et privés de l'objet et le nom de la classe sont convertis en un flux de données d'octets, écrit ensuite dans un flux de données. ​

Lorsque l'objet est désérialisé, un clone exact de l'objet d'origine est créé.​

Le support de stockage peut être, par exemple, un fichier binaire , un fichier JSON ou un fichier XML.​

# Classe Serializer

Nous commençons par créer un nouveau dossier "Outils" puis y créer une nouvelle classe abstraite (indiqué par le mot clé *abstract*) ***Serializer***. Abstraite signifie que la classe n'est pas instanciable.

Cette classe va comprendre deux méthodes :

- **serialize()** qui va enregistrer les données:

```c#
public static void serialize(string nomFichier, Object obj)
{
    string fichier = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData), nomFichier);

	if(File.Exists(fichier))
    {
        File.Delete(fichier);
    }

    //creation du flux pour l'ecriture du fichier 
    FileStream fluxCreate = new FileStream(fichier, FileMode.Create, FileAccess.Write);

    //creation d'un objet pour le formatage binaires des informations
    BinaryFormatter bf = new BinaryFormatter();

    try
    {
        //Serialisation des objets 
        bf.Serialize(fluxCreate, obj)
        //fermeture du flux
        fluxCreate.Close();
    } catch (Exception e)
    {
        //Erreur de serialisation 
        Toast.MakeText(this, "Erreur d'enregistrement des données : " + e,       ToastLength.Long).Show();
    }

           
}
```

- **deserialize()** qui va les charger:

```c#
public static Object deserialize(string nomFichier)
{

    Object obj = null; 
    string fichier = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData), nomFichier);

    if (File.Exists(fichier))
    {
        //creation du flux pour l'ecriture du fichier 
        FileStream fluxCreate = new FileStream(fichier, FileMode.Open
        //creation d'un objet pour le formatage binaires des informations
        BinaryFormatter bf = new BinaryFormatter(
        try
        {
            //Serialisation des objets 
            obj = bf.Deserialize(fluxCreate
            //fermeture du flux
            fluxCreate.Close();
        }
        catch (Exception e)
        {
            //Erreur de serialisation 
            Toast.MakeText(this, "Erreur de recuperation des données : " + e, ToastLength.Long).Show();
        }
    }

            
    return obj;

}
```

Afin d'indiquer au compilateur quels données sont serialisables, il faut ajouter `[Serializable]` avant la déclaration de la classe ***Profil*** :
```c#
[Serializable]
public class Profil
{
    private int sexe; //0 pr femme et 1 pr homme
    private int poids; //kg
    [...]
}
```

On va ensuite créer une variable privée dans la classe ***Controle*** qui va stocker le nom du fichier utilisé pour la serialisation :

```c#
private static string nomFichier = "saveprofil"; //fichier pour serialisation
```

Pour permettre à la vue (*MainActivity.cs*) de récupérer les données, nous allons dans un premier temps créer des getters dans la classe ***Controle*** :

```c#
//getter pour la serialisation
public int GetPoids() => profil == null ? 0 : profil.GetPoids();
public int GetTaille() => profil == null ? 0 : profil.GetTaille();
public int GetAge() => profil == null ? 0 : profil.GetAge();
public int GetSexe() => profil == null ? 0 : profil.GetSexe();
```

Nous allons ensuite créer une méthode **RecupProfil()** dans la classe ***MainActivity*** qui va valoriser les valeurs des éléments graphiques avec les données récupérer grâce à la serialisation. (Exemple => Le texte de EditTextPoids va recevoir le poids du dernier utilisateur avant la fermeture de l'application)

```c#
private void RecupProfil()
{
    if (controle.GetPoids() != 0)
    {
        txtPoids.Text = controle.GetPoids().ToString();
        txtAge.Text = controle.GetAge().ToString();
        txtTaille.Text = controle.GetTaille().ToString()
        radioButtonF.Checked = true;
        if (controle.GetSexe() == 1)
        {
            radioButtonM.Checked = true;
        
        double img = controle.GetImg();
        string message = controle.GetMessage();
        if (message == "Parfait")
        
            imageViewSmiley.SetImageResource(Resource.Drawable.Smiley_Ok);
            textViewImg.SetTextColor(Android.Graphics.Color.DarkGreen);
        }
        else if (message == "Trop Maigre")
        {
            imageViewSmiley.SetImageResource(Resource.Drawable.Smiley_No);
            textViewImg.SetTextColor(Android.Graphics.Color.DarkRed);
        }
        else if (message == "Surpoids")
        {
            imageViewSmiley.SetImageResource(Resource.Drawable.Smiley_PasTop);
            textViewImg.SetTextColor(Android.Graphics.Color.DarkRed);
        }
        textViewImg.Text = String.Format("{0:0.00}", img) + "%" + message;
    }
}
```

Enfin, on va appeler cette méthode dans Init() afin que l'on récupère les données à chaques ouvertures de la vue.