# Overpass — TryHackMe

**Data:** 03/08/2026  
**Autore:** Daniel Marceddu  
**Difficoltà:** Facile  
**Piattaforma:** TryHackMe  
**IP Target:** 10.114.168.74

---

## Executive Summary

Overpass è una macchina Linux che presenta una vulnerabilità di broken authentication nel pannello di amministrazione web e una privilege escalation tramite cron job con DNS spoofing su `/etc/hosts` scrivibile.

---

## 1. Reconnaissance

### Port Scan
```bash
nmap -sC -sV -oN nmap/initial.txt 10.114.168.74
