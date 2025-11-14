# 🛡️ TP1 – ACL Standard

## 🎯 Objectif

Mettre en œuvre une ACL standard pour **empêcher une machine (192.168.1.7)** d’accéder à un autre réseau (192.168.2.0), tout en autorisant les autres hôtes à communiquer normalement.

---

##  Topologie du réseau

- **192.168.1.0/24** : PC1 (192.168.1.7), PC3 (192.168.1.8)
- **192.168.2.0/24** : PC2 (192.168.2.2), Interface routeur (192.168.2.1)
- **Routeur** :
  - `fa0/0` : connecté au réseau 1.0
  - `fa0/1` : connecté au réseau 2.0

*Capture 1 – Schéma de la topologie*  

<img width="875" height="313" alt="1" src="https://github.com/user-attachments/assets/e0ff8320-c22d-49d3-b202-b8c5aa962c5c" />


##  Configuration

### 🔹 Étape 1 : Création de l'ACL

```bash
access-list 1 deny host 192.168.1.7
access-list 1 permit any
```

### 🔹 Étape 2 : Application sur l'interface

```bash
interface fa0/0
ip access-group 1 in
```

📷 *Capture 2 – Configuration de l'ACL*  
<img width="377" height="85" alt="2" src="https://github.com/user-attachments/assets/e117ebb7-dab8-4cba-bf4b-d6ab68c0f8e5" />

---


### Sur quelle interface appliquer l'ACL ? `in` ou `out` ?
✅ **Fa0/0 en `in`**. Le trafic vient du réseau 1.0. L’ACL standard filtre uniquement **l’IP source**.

### Que se passe-t-il si on n’ajoute pas la règle `permit any` ?
⛔ Tout sera bloqué à cause de la **règle implicite `deny all`**.

### Pourquoi une ACL **standard** suffit ?
✅ Car on veut bloquer **l’IP source**, peu importe la destination ou le protocole.

---

## Tests et vérifications

### 🔸 Test 1 – Ping depuis 192.168.1.7 vers 192.168.2.2
📷 *Capture 3 – Ping échoué depuis 1.7 (bloqué)*  
<img width="398" height="239" alt="3" src="https://github.com/user-attachments/assets/afde6a02-d083-4b70-a4ef-843aad203079" />  
<img width="359" height="77" alt="Capture d’écran 2025-11-14 233757" src="https://github.com/user-attachments/assets/a98e7952-ba2b-4547-9a6d-58f82f2b6117" />

```bash
Reply from 192.168.1.1: Destination host unreachable.
```

### 🔸 Test 2 – Ping depuis 192.168.1.8 vers 192.168.2.1
📷 *Capture 4 – Ping réussi depuis 1.8*  
<img width="337" height="131" alt="5" src="https://github.com/user-attachments/assets/918b1a1f-f37a-4c23-8585-e63e9273d7cd" />

```bash
Reply from 192.168.2.1: bytes=32 time=1ms TTL=255
```

### 🔸 Vérification de l'ACL
📷 *Capture 5 – Résultat de `show access-lists`*  
<img width="259" height="86" alt="6" src="https://github.com/user-attachments/assets/7d9270a6-1bec-4fba-88a7-c8062430d88d" />

```bash
Standard IP access list 1
10 deny host 192.168.1.7 (4 match(es))
20 permit any (4 match(es))
```

---

## 📌 Résumé des tests

| Test                        | Résultat | Explication         |
|----------------------------|----------|----------------------|
| Ping 1.7 ➜ 2.2             | ❌ Échec | Trafic bloqué        |
| Ping 1.8 ➜ 2.1             | ✅ OK    | Autorisé             |
| `show access-list`         | ✅ 4 match | ACL a bien filtré    |

---

## 🧠 Ce qu’on a compris

- ACL standard filtre **l’IP source uniquement**
- Placement stratégique : **proche de la source**
- Règle `permit` obligatoire si on ne veut pas tout bloquer
- `show access-list` donne des stats utiles sur les paquets bloqués

---

## ✅ Conclusion

Ce TP m’a permis de :
- comprendre le filtrage IP avec ACL standard
- appliquer des règles sur des interfaces
- tester et diagnostiquer avec précision le comportement réseau

---

> 📁 *Fichier config routeur : [routeur_tp1_config.txt](acl-cisco/ACL-STANDARD/routeur_tp1_config.txt)*
