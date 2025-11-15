# 🚫 ACL Étendue

## 🎯 Objectif

Empêcher la machine **192.168.1.8** (PC3) de communiquer avec la machine **192.168.2.8** (PC4), en utilisant une **ACL étendue nommée** `filtreExt`.

---

## 🧱 Topologie du réseau

- **Réseau 1.0/24** :
  - PC0 : 192.168.1.7
  - PC3 : 192.168.1.8
- **Réseau 2.0/24** :
  - PC2 : 192.168.2.7
  - PC4 : 192.168.2.8
- **Routeur** :
  - `fa0/0` : vers 192.168.1.0
  - `fa0/1` : vers 192.168.2.0

📷 *Capture 1 – Schéma de la topologie utilisée*  
<img width="452" height="283" alt="1" src="https://github.com/user-attachments/assets/b46916c6-98d2-4acb-8cc1-597da55615cf" />

---

## ⚙️ Configuration de l’ACL

### 🔹 Étape 1 – Définir l'ACL étendue

```bash
ip access-list extended filtreExt
deny ip host 192.168.1.8 host 192.168.2.8
permit ip any any
```

### 🔹 Étape 2 – Appliquer l’ACL sur l’interface

```bash
interface fastEthernet 0/1
ip access-group filtreExt out
```

📷 *Capture 2 – Configuration de l’ACL étendue*  
<img width="368" height="127" alt="2" src="https://github.com/user-attachments/assets/3ae196a0-2e13-467c-af61-de1748a21547" />

---

## 

### Pourquoi appliquer l’ACL sur **fa0/1** en **out** ?
✅ Parce que la **destination est dans le réseau 2.0**, donc le trafic sort par l’interface `fa0/1`.  
L’ACL étendue peut filtrer en fonction de la source **et** de la destination, donc on choisit **l’interface de sortie**.

---

## 🧪 Vérification

### 🔸 Test 1 – Ping depuis 192.168.1.8 ➜ 192.168.2.8
📷 *Capture 3 – Résultat : Ping bloqué*  
<img width="380" height="173" alt="3" src="https://github.com/user-attachments/assets/0e9c7ca9-cd6c-42d6-9a4b-6143a688633a" />


### 🔸 Test 2 – Ping depuis 192.168.1.7 ➜ 192.168.2.8
📷 *Capture 4 – Résultat : Ping réussi*  
<img width="282" height="68" alt="4" src="https://github.com/user-attachments/assets/f8a3c26d-27c2-4702-8ce8-ffb726b79544" />


### 🔸 Vérification avec `show access-lists`
📷 *Capture 5 – Résultat avec nombre de MATCH sur la règle `deny`*  

<img width="320" height="131" alt="5" src="https://github.com/user-attachments/assets/b9914e9a-cdda-42c9-9552-f705692bf60e" />


---

## 📌 Résumé des tests

| Test                        | Résultat | Explication                    |
|----------------------------|----------|--------------------------------|
| Ping 1.8 ➜ 2.8             | ❌ Bloqué | Interdit par ACL étendue       |
| Ping 1.7 ➜ 2.8             | ✅ OK     | Non concerné par l’ACL         |
| `show access-lists`        | ✅ Match | Paquets ICMP bloqués comptés   |

---

## 🧠 Ce qu’on a compris

- Les **ACL étendues** permettent de filtrer **source + destination + protocole**
- Elles doivent être placées **proche de la destination** (ici `out`)
- Les tests de ping permettent de confirmer l’efficacité
- `show access-lists` permet de voir les paquets bloqués (MATCH)

---

## ✅ Conclusion

Ce travail m’a permis de :
- Apprendre à créer une **ACL étendue nommée**
- Comprendre comment bloquer la communication entre deux hôtes spécifiques
- Appliquer la règle sur la bonne interface (`fa0/1`) en **sortie**
- Vérifier les effets avec des outils de diagnostic (ping, show access-lists)

---

> 📁 *Fichier config routeur : [routeur_tp2_config.txt](./routeur_tp2_config.txt)*
