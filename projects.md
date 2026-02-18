# Projects

FLP maintains several interconnected open-source projects. Here's the map.

## CourtListener

**The main application.** A platform for searching and accessing court opinions, oral arguments, judges, and financial disclosures.

- **Stack**: Python, Django, PostgreSQL, Solr, Redis, Docker
- **Repo**: [freelawproject/courtlistener](https://github.com/freelawproject/courtlistener)
- **Wiki**: [CourtListener Wiki](https://github.com/freelawproject/courtlistener/wiki) (project-specific dev setup)

This is where most new developers start. The wiki has detailed Docker setup and architecture docs.

## Juriscraper

A scraping framework that collects court opinions and oral arguments from court websites across the US.

- **Stack**: Python
- **Repo**: [freelawproject/juriscraper](https://github.com/freelawproject/juriscraper)

Juriscraper feeds data into CourtListener. If you're working on adding or fixing a court scraper, this is where you'll be.

## RECAP Extensions

Browser extensions that liberate PACER documents and make them freely available.

- **Stack**: JavaScript (browser extension)
- **Repo**: [freelawproject/recap-chrome](https://github.com/freelawproject/recap-chrome)

## Doctor

A document conversion microservice that handles PDF extraction, thumbnail generation, and other document processing tasks.

- **Stack**: Python
- **Repo**: [freelawproject/doctor](https://github.com/freelawproject/doctor)

## Litigant Portal

An access-to-justice tool that helps people navigate the legal system.

- **Stack**: JavaScript
- **Repo**: [freelawproject/litigant-portal](https://github.com/freelawproject/litigant-portal)

## Judge Pics

Community-maintained collection of judge portraits used on CourtListener.

- **Repo**: [freelawproject/judge-pics](https://github.com/freelawproject/judge-pics)

## Seal Rookery

Community-maintained collection of court seals and logos.

- **Repo**: [freelawproject/seal-rookery](https://github.com/freelawproject/seal-rookery)

## free.law

FLP's website and blog.

- **Stack**: MDX
- **Repo**: [freelawproject/free.law](https://github.com/freelawproject/free.law)

## HR

Policies, handbook, and HR documentation.

- **Repo**: [freelawproject/hr](https://github.com/freelawproject/hr)

See [Resources](resources.md) for direct links to specific policies.

## How the Pieces Fit Together

```
                    ┌─────────────┐
                    │  RECAP Ext  │
                    │  (browser)  │
                    └──────┬──────┘
                           │ uploads
                           ▼
┌─────────────┐    ┌──────────────┐    ┌──────────┐
│ Juriscraper │───▶│ CourtListener│◀──▶│  Doctor  │
│ (scraping)  │    │  (main app)  │    │ (doc svc)│
└─────────────┘    └──────────────┘    └──────────┘
                           │
                    ┌──────┴──────┐
                    ▼             ▼
              ┌──────────┐ ┌──────────┐
              │Judge Pics│ │Seal Rook.│
              └──────────┘ └──────────┘
```

- **Juriscraper** scrapes courts and feeds opinions/oral arguments into CourtListener
- **RECAP** uploads PACER documents from users' browsers into CourtListener
- **Doctor** provides document conversion services that CourtListener calls
- **Judge Pics** and **Seal Rookery** provide images displayed in CourtListener
