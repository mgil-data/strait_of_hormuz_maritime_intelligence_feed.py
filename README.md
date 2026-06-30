# strait_of_hormuz_maritime_intelligence_feed
Real-time maritime OSINT pipeline monitoring the Strait of Hormuz crisis, built on Palantir Foundry.

# Hormuz Maritime Intelligence Pipeline

Real-time OSINT pipeline monitoring maritime activity in the Strait of Hormuz, built on Palantir Foundry.

## About

This is an independent project exploring how open-source data can be transformed into structured, actionable intelligence around one of the world's most strategically significant maritime chokepoints: the Strait of Hormuz.

Built entirely on open-source data, the pipeline ingests, processes, and links vessel, economic, and geopolitical data to support real-world operational analysis.

## Status

- Core vessel tracking pipeline live (identification, positioning, ownership, inspections, port events)
- Expanding into economic and geopolitical data sources, unified through a shared ontology

Started June 2026, actively under development.

## Data Sources

**Vessel & Maritime Activity**
- Vessel identification, positioning, and movement (AIS-based)
- Port arrival and departure events
- Vessel ownership and management records
- Inspection history
- Vessel casualty/incident records

**Economic & Geopolitical (in progress)**
- Market and commodity pricing data
- Weather conditions
- Sanctions updates
- Oil tanker movement and cargo intelligence

All sources are linked through a shared Foundry ontology, connecting vessels to ports, owners, sanctions status, and broader geopolitical events.

## Technical Architecture

- **Pipeline type:** batch processing, scheduled builds via Foundry pipelines
- **Latency:** low-latency, near real-time ingestion, with batch refresh intervals determined by each source's update cadence
- **Data modeling:** from raw ingestion to cleaned/transformed tables; then, to ontology objects and links
- **Tech stack:** Python (Polars/Pandas) for ingestion and transformation, REST APIs for source integration, Foundry and AIP for orchestration and ontology modeling

## Goals

This project was built to demonstrate hands-on proficiency with Foundry and AIP tools, independent problem-solving, and the ability to transform fragmented open-source data into a coherent, operationally meaningful intelligence picture. To showcase these capabilities, I chose a topic at the center of current geopolitical tensions.

## Notes

Any data shown in published screenshots or documentation has been appropriately safeguarded where necessary to protect sensitive vessel-level information. The focus of this project is the methodology and architecture.
