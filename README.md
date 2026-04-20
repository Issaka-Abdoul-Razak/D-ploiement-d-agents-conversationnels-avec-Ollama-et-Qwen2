# Sahel Agent

Un agent conversationnel local basé sur le pattern **ReAct** (Reasoning + Acting), propulsé par [Ollama](https://ollama.com/) et exposé via une interface web Flask. L'agent est capable de raisonner, d'utiliser des outils (recherche web, calcul, date, analyse de documents) et de mémoriser les conversations.

---

## 📁 Structure du projet

```
sahel-agent/
├── agent.py              # Moteur de l'agent (logique ReAct + outils)
├── app.py                # Serveur web Flask + routes API
├── install.bat           # Script d'installation Windows
├── requirements.txt      # Dépendances Python
├── templates/
│   └── index.html        # Interface utilisateur web
└── memory/
    ├── documents/        # Documents uploadés (PDF, TXT, MD)
    └── short_term/
        └── history.json  # Historique des conversations
```

---

##  Fonctionnalités

- **Agent ReAct** : raisonnement itératif en boucle Pensée → Action → Observation → Réponse
- **Outils intégrés** :
  - `web_search` — Recherche en temps réel via DuckDuckGo
  - `calculator` — Calculs mathématiques sécurisés
  - `datetime` — Heure et date actuelles
  - `document_analyzer` — Analyse de fichiers PDF/TXT/MD uploadés
- **Mémoire court terme** : historique de conversation persisté en JSON
- **Upload de documents** : support des formats PDF, TXT et Markdown (jusqu'à 16 Mo)
- **Interface web** : chat accessible depuis le navigateur
- **100 % local** : aucune clé API requise — tout tourne via Ollama

---

## 🛠️ Prérequis

- Python 3.10+
- [Ollama](https://ollama.com/) installé et en cours d'exécution
- Un modèle Ollama téléchargé (ex. `mistral`, `llama3`, `qwen2.5`)

---

## 🚀 Installation

### Windows (rapide)

```bat
install.bat
```

### Manuelle (cross-platform)

```bash
# Cloner / décompresser le projet, puis :
cd sahel-agent

# Créer et activer l'environnement virtuel
python -m venv venv
source venv/bin/activate        # Linux/macOS
venv\Scripts\activate.bat       # Windows

# Installer les dépendances
pip install -r requirements.txt
```

**Dépendances principales :**

| Package | Version | Rôle |
|---|---|---|
| flask | 3.0.0 | Serveur web |
| ollama | 0.1.7 | Client LLM local |
| duckduckgo-search | 5.3.0 | Recherche web |
| PyPDF2 | 3.0.1 | Extraction texte PDF |
| werkzeug | 3.1.5 | Utilitaires Flask |

---

## ▶️ Lancement

1. S'assurer qu'Ollama tourne et qu'un modèle est disponible :
   ```bash
   ollama pull mistral   # ou le modèle de votre choix
   ollama serve
   ```

2. Démarrer l'application Flask :
   ```bash
   python app.py
   ```

3. Ouvrir le navigateur à l'adresse : `http://localhost:5000`

---

## 💬 Utilisation

- Tapez votre message dans le chat et envoyez.
- L'agent raisonne en plusieurs étapes (max 5 itérations) avant de répondre.
- Pour analyser un document, uploadez un fichier PDF, TXT ou MD depuis l'interface, puis interrogez l'agent à son sujet.
- Toutes les réponses sont en **français**.

---

## ⚙️ Configuration

Le modèle LLM utilisé et les paramètres de génération peuvent être ajustés directement dans `agent.py` :

```python
self.model = "mistral"        # Nom du modèle Ollama
self.max_iterations = 5       # Nombre max d'itérations ReAct
# options dans ollama.chat() :
# temperature: 0.3
# num_ctx: 4096
```

La taille maximale d'upload est configurable dans `app.py` :

```python
app.config['MAX_CONTENT_LENGTH'] = 16 * 1024 * 1024  # 16 Mo
```

---

## 🧠 Architecture ReAct

```
Utilisateur
    │
    ▼
[PENSÉE]  ← Le LLM analyse la demande
    │
    ▼
[ACTION]  ← Appel d'un outil (web_search, calculator, etc.)
    │
    ▼
[OBSERVATION] ← Résultat de l'outil réinjecté dans le contexte
    │
    └─── (répété jusqu'à max_iterations ou réponse finale)
    │
    ▼
[RÉPONSE FINALE] → Affichée à l'utilisateur
```

---

## 📝 Notes

- L'extraction de texte des PDF est limitée aux **5 000 premiers caractères**.
- L'historique de conversation est sauvegardé dans `memory/short_term/history.json` à chaque échange.
- Le projet inclut un virtualenv Windows pré-configuré (`venv/`) ; sur Linux/macOS, recréer l'environnement avec `python -m venv venv`.

---

## 📄 Licence

Projet personnel — usage libre pour des fins académiques et d'expérimentation.
