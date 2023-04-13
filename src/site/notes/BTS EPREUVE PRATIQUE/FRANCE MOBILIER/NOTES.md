---
{"dg-publish":true,"permalink":"/bts-epreuve-pratique/france-mobilier/notes/","dgPassFrontmatter":true}
---


# Ajout de l'attribut pro_gamme dans la table prodiut
```sql
alter table produit
add pro_gamme int;
alter table produit
ADD FOREIGN KEY (pro_gamme) REFERENCES gamme(gam_id);
```
# Ajout classe métier "Gamme.php"

```php
<?php
    class Gamme
    {
        private $gam_id;
        private $gam_libelle;
        public function __construct($id,$libelle)
        {
            $this->gam_id=$id;
            $this->gam_libelle=$libelle;
        }
        public function GetId()
        {
            return $this->gam_id;
        }
        public function GetLibelle()
        {
            return $this->gam_libelle;
        }
    }
?>
```

# Ajout du modele "m_gamme.php"

```php
<?php
    require_once "m_generique.php";
    require_once "metiers/Gamme.php";
    class m_gamme extends m_generique
    {
        public function GetListe()
        {
            $resultat=array();
            $this->connexion();
            $req="select * from gamme order by gam_libelle";
            $res=mysqli_query($this->GetCnx(),$req);
            $ligne=mysqli_fetch_assoc($res);
            while ($ligne)
            {
                $gamme=new Gamme($ligne["gam_id"],$ligne["gam_libelle"]);
                $resultat[]=$gamme;
                $ligne=mysqli_fetch_assoc($res);
            }
            $this->deconnexion();
            return $resultat;
        }
        public function GetGamme($id)
        {
            $resultat=null;
            $this->connexion();
            $req="select * from gamme where gam_id=".$id;
            $res=mysqli_query($this->GetCnx(),$req);
            $ligne=mysqli_fetch_assoc($res);
            if($ligne)
            {
                $gamme=new Gamme($ligne["gam_id"],$ligne["gam_libelle"]);
                $resultat=$gamme;
            }
            $this->deconnexion();
            return $resultat;
        }
    }
?>
```

# Ajout du choix de la gamme dans le menu

```php
<select name="gamme" size="1">

            <option selected value="0">Toutes les gammes</option>

            <?php

                foreach ($this->data['lesGammes'] as $uneGamme)

                {

                    echo '<option value="'.$uneGamme->GetId().'">'.$uneGamme->GetLibelle().'</option>';

                }

            ?>

        </select>
``` 
# Mettre à jour modele "m_produit.php"

```php
if ($categ==0 && $gamme==0)

            {

                $req="select * from produit";

            }

            else if ($gamme==0)

            {

                $req="select * from produit where pro_categorie=".$categ;

            }

            else if ($categ==0)

            {

                $req="select * from produit where pro_gamme=".$gamme;

            }

            else if ($categ!=0 && $gamme!=0)

            {

                $req="select * from produit where pro_categorie=".$categ;

            }
```
# Mettre à jour FillData() dans controleur "c_menu.php"

```php
public function FillData(&$data)
        {
            $data['lesGammes']=$this->modele_gamme->GetListe();
        }
```
# Mettre à jour le controleur "c_consulterProduits.php"

```php
<?php
    require_once "modeles/m_gamme.php";
    class c_consulterProduits
    {
        private $modele_gamme;
        public function __construct()
        {
            $this->modele_gamme=new m_gamme();
        }
        public function action_accueil()
        {
            $this->data['lesGammes']=$this->modele_gamme->GetListe();
        }

        public function action_listeProduits($idCategorie, $idGamme)
        {
            $this->data['laGamme']=$this->modele_gamme->GetGamme($idGamme);
            $this->data['lesProduits']=$this->modele_produit->GetListe($idCategorie, $idGamme);     // retourne tous les produits si $idCategorie==0
            require_once "vues/v_listePdt.php";
        }
    }
?>
```

# Mettre à jour la vue 

```php
if (is_null($this->data['laCategorie']) && is_null($this->data['laGamme']))
            {
                echo '<h2>Tous les produits</h2>';
            }
            else if (is_null($this->data['laCategorie']))
            {
                echo '<h2>Gamme '.$this->data['laGamme']->GetLibelle().'</h2>';
            }
            else if (is_null($this->data['laGamme']))
            {
                echo '<h2>Catégorie '.$this->data['laCategorie']->GetLibelle().'</h2>';
            }
            else
            {
                echo '<h2>Catégorie '.$this->data['laCategorie']->GetLibelle().'</h2>';
            }
```
# Ajout d'un attribut "abo_gam" dans table abonne
```sql
alter table abonne
add abo_gam int;
alter table abonne
ADD FOREIGN KEY (abo_gam) REFERENCES gamme(gam_id);
```

