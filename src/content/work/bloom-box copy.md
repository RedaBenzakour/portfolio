---
title: DynamicAgentRANPGE — API LLM pour plateforme d'apprentissage (FastAPI)
publishDate: 2025-09-13 00:00:00
img: /assets/stock-5.png
img_alt: Plateforme d’apprentissage IA avec chat, évaluation et indexation documentaire
description: |
  API FastAPI pour une plateforme d'apprentissage intelligente pilotée par des agents LLM. 
  Elle gère l'authentification, le chat avec mémoire, l'évaluation automatique, la génération 
  de questions et l'indexation documentaire (LlamaIndex) en utilisant MongoDB, Redis et Azure OpenAI.
  
tags:
  - fastapi
  - llm
  - mongodb
  - redis
  - llamaindex
  - azure-openai
---

Ce projet **DynamicAgentRANPGE** a été conçu comme un backend unifié pour des cas d’usage pédagogiques avancés : 
tutorat conversationnel, évaluation automatisée, révisions guidées et recherche contextuelle dans les documents de cours. 
L’objectif était de **personnaliser l’apprentissage** tout en **automatisant** les tâches répétitives (notation, génération de questions) 
et en **centralisant** les services derrière une API claire et maintenable.

#### Technologies Utilisées

- **Backend** : FastAPI (Python)
- **LLM** : Azure OpenAI (chat/completions)
- **RAG** : LlamaIndex (VectorStoreIndex, retrieval)
- **Base de Données** : MongoDB (persistance conversations/users)
- **Cache/État** : Redis
- **Auth** : JWT (access/refresh)

#### Endpoints Principaux

- `POST /api/auth/login` : Authentification (JWT)
- `GET /api/users/me` : Informations de l’utilisateur courant
- `POST /api/chat` : Message → réponse LLM + mémoire conversationnelle (MongoDB)
- `POST /api/evaluation/grade` : Évaluation/notation automatique d’une réponse d’étudiant
- `POST /api/evaluation/generate-questions` : Génération de questions d’entraînement
- `GET /api/admin/index/rebuild` : Reconstruction de l’index documentaire (LlamaIndex)

#### Administration

- **Gestion du contenu** : reconstruction de l’index, contrôle des modules/ressources
- **Suivi** : consultation des conversations et des journaux d’évaluations
- **Sécurité** : rôles/permissions via JWT + dépendances FastAPI

#### Config / Services

- `app/config.py` : variables d’environnement (Azure OpenAI, MongoDB, Redis, JWT)
- `app/services/chat/` : ChatService (mémoire, RAG, prompts)
- `app/services/evaluation/` : EvaluationService (grading, feedback, progression)
- `app/services/index/` : IndexService (LlamaIndex)
- `app/services/external/` : clients (Azure OpenAI, auth), Redis
- `app/repositories/` : DAO MongoDB (conversations, messages, users)
- `app/models/` : Pydantic + modèles stockés

#### Base de Données

- **Collections** : `users`, `conversations`, `messages`, `grades`, `progress`
- **Conversation** : historique tronqué (fenêtre récente) + contexte documentaire attaché

#### Assets

- **Image de couverture** : `/assets/dynamicagentrangep-cover.png` (fournie)
- **Captures (optionnel)** : écrans Swagger `/docs` et exemples de payloads

#### Fonctionnalités Clés

- Chat intelligent avec **mémoire** par utilisateur (persistance MongoDB)
- **Évaluation** automatique (score + feedback + pistes d’amélioration)
- **Génération de questions** de révision
- **Indexation & recherche** dans les documents (LlamaIndex) pour réponses sourcées
- **Administration** et sécurisation via JWT

#### Démonstration

Lancer le serveur puis visiter **`/docs`** (Swagger) pour tester les endpoints en direct.  
Exemples d’appels fournis dans la section code ci-dessous.

#### Conclusion

DynamicAgentRANPGE montre un backend IA **modulaire** et **scalable** pour l’éducation, 
connectant LLM, mémoire, évaluation et recherche documentaire au sein d’une API propre et extensible.

#### Lien GitHub

*(À ajouter si le dépôt est public — sinon fournir un zip ou un lien privé.)*


#### app/api/chat_routes.py (extrait “comme celui-là”, adapté au projet)
```python
from fastapi import APIRouter, Depends, HTTPException
from pydantic import BaseModel
from typing import Optional, Dict, Any
from app.services.chat.chat_service import get_chat_service
from app.services.external.auth_service import auth_service

router = APIRouter(prefix="/api/chat", tags=["chat"])

class ChatRequest(BaseModel):
    message: str
    conversation_id: Optional[str] = None  # pour reprendre la même conversation

@router.post("")
async def chat_endpoint(payload: ChatRequest, user: Dict[str, Any] = Depends(auth_service.require_user)):
    """
    Reçoit un message utilisateur, appelle l’agent LLM (Azure OpenAI) avec mémoire + RAG,
    persiste l’historique en MongoDB, puis renvoie la réponse et l’ID de conversation.
    """
    try:
        result = await get_chat_service().process_chat_message(
            user_id=user["id"],
            message=payload.message,
            conversation_id=payload.conversation_id
        )
        return {"ok": True, "data": result}
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
