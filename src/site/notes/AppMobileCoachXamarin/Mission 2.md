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

Afin d'indiquer au compilateur quels données sont serialisables, il faut ajouter `[Serializable]` 