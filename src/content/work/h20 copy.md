---
title: Generative AI Prod — API IA Générative (FastAPI)
publishDate: 2025-09-13 00:00:00
img: /assets/stock-6.png
img_alt: Illustration d’une API de chat et d’analyse IA en production
description: |
  API FastAPI de production pour l’IA générative. 
  Elle fournit des services de chat conversationnel, d’analyse de texte, 
  de gestion des conversations et d’administration des utilisateurs, 
  en utilisant MongoDB et un système de cache Redis.
  
tags:
  - fastapi
  - ai
  - mongodb
  - redis
  - production
---

Ce projet **Generative AI Prod** a été conçu comme une base **prête pour la production** d’une API IA : 
un serveur FastAPI qui gère le **chat IA**, l’**analyse automatisée de texte**, la **sauvegarde/recherche de conversations** 
et les fonctionnalités **d’administration**.

#### Technologies Utilisées

- **Backend** : FastAPI (Python)
- **Base de Données** : MongoDB (stockage des conversations, utilisateurs)
- **Cache** : Redis (accélération et sessions)
- **Auth** : JWT + services admin
- **Déploiement** : Procfile (Heroku ou équivalent), runtime.txt, startup.sh

#### Endpoints Principaux

- `POST /api/chat` : Chat IA (gestion par `chat_router.py` et `chat_service.py`)
- `POST /api/analyse` : Analyse de texte/document (géré par `analyse_router.py` + `analysis_service.py`)
- `GET  /api/conversations/{id}` : Récupération de conversations sauvegardées
- `POST /api/admin/...` : Fonctions administratives (gestion utilisateurs, logs, etc.)

#### Architecture (dossiers)

- `main.py` : point d’entrée FastAPI
- `models/` : schémas de données (Pydantic + Mongo)
- `routes/` : routes FastAPI (`chat_router.py`, `analyse_router.py`, `admin_router.py`)
- `services/` : logique métier (`chat_service`, `analysis_service`, `admin_services`, `saveConversation_service`)
- `utils/` : configuration du cache (Redis) et prompts
- `requirements.txt` : dépendances
- `Procfile` / `runtime.txt` : config de déploiement cloud
- `startup.sh` : script de démarrage

#### Base de Données

- **MongoDB** : 
  - `users` (infos et droits d’accès)
  - `conversations` (messages, contexte)
  - `analysis` (résultats d’analyse de texte)

#### Fonctionnalités Clés

- Chat conversationnel basé IA
- Analyse de texte automatisée
- Sauvegarde et récupération de conversations
- Interface d’administration
- Cache/accélération via Redis
- Déploiement cloud (Heroku ou équivalent)

#### Démonstration

L’API expose sa documentation Swagger à l’URL `/docs`, permettant de tester facilement les endpoints 
(chat, analyse, administration).

#### Conclusion

**Generative AI Prod** propose une API robuste pour déployer des services IA en production : 
conversation, analyse, gestion et persistance, avec une architecture claire et modulaire.

#### Lien GitHub

*(À ajouter si le dépôt est public.)*


#### routes/chat_router.py (extrait représentatif “comme celui-là”)
```python
from fastapi import APIRouter, Depends, HTTPException
from pydantic import BaseModel
from typing import Dict, Any, Optional
from services.chat_service import ChatService
from services.mongodb_connection import get_database

router = APIRouter(prefix="/api/chat", tags=["chat"])

class ChatRequest(BaseModel):
    message: str
    conversation_id: Optional[str] = None

@router.post("")
async def chat_endpoint(req: ChatRequest, db=Depends(get_database)):
    """
    Reçoit un message, appelle le ChatService, sauvegarde la conversation en MongoDB,
    puis renvoie la réponse générée par le modèle IA.
    """
    try:
        chat_service = ChatService(db)
        result = await chat_service.process(req.message, req.conversation_id)
        return {"ok": True, "data": result}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
