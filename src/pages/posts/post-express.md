---
layout: ../../layouts/MarkdownPostLayout.astro
title: Express App page config
author: Frtav
description: "Declarations Order"
image:
    url: "https://docs.astro.build/assets/rays.webp"
    alt: "The Astro logo on a dark background with rainbow rays."
pubDate: 2024-11-28
tags: ["astro", "learning in public", "setbacks", "community"]
---

```js
// CONFIGURACION
// 1. Configuración y dependencias
const config = require('./utils/config')
const express = require('express')
const app = express()
const cors = require('cors')
const notesRouter = require('./controllers/notes')
const middleware = require('./utils/middleware')
const logger = require('./utils/logger')
const mongoose = require('mongoose')

// 2. Configuración de Mongoose (se debe conectar a la BD antes de comenzar a manejar solicitudes)
mongoose.set('strictQuery', false)
logger.info('connecting to', config.MONGODB_URI)

mongoose.connect(config.MONGODB_URI)
.then(() => {
logger.info('connected to MongoDB')
})
.catch((error) => {
logger.error('error connecting to MongoDB:', error.message)
})

// MIDDLEWARE global
app.use(cors())
app.use(express.static('dist'))
app.use(express.json())
app.use(middleware.requestLogger)

// RUTAS
app.use('/api/notes', notesRouter)

//MIDDLEWARE manejo de rutas (deben ir despues de las rutas)
app.use(middleware.unknownEndpoint)
app.use(middleware.errorHandler)

module.exports = app
```