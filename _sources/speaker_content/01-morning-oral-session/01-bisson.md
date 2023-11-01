---
jupytext:
    text_representation:
        extension: .md
        format_name: myst
kernelspec:
    display_name: Python 3
    language: python
    name: python3
---
# 01 - 	On a QUEST (Query, Unify, Explore SpatioTemporal) to Accelerate ICESat-2 Applications in Ocean Science via icepyx

Presented by: Kelsey Bisson

## Abstract 
Collaboration and inclusivity will be critical for tackling modern environmental challenges resulting from increased anthropogenic stressors. Even as we celebrate 2023 as the Year of Open Source Science, many scientific disciplines - and thus their technologies, tools, and expertise - remain siloed. The ICESat-2 (Ice, Cloud, and land Elevation Satellite 2) was launched in 2018 with a strong focus towards cryospheric investigations. However, the sensor’s contributions to advances among the vegetation and oceanography communities are nontrivial. New environmental discoveries are made possible by exposing the mission and its unique capabilities to other disciplines.

The QUEST (Query, Unify, Explore SpatioTemporal) Python software library aims to bridge this divide. QUEST is a submodule of icepyx, a community of data managers, users, and developers and a software library providing tooling for ICESat-2 data access, visualization, processing, and analysis locally and in the cloud. The QUEST module capitalizes on the object-oriented properties of Python (and thus icepyx) to create a framework for easily integrating additional datasets into a single workflow. The motivating use case for developing QUEST came from oceanography and aims to combine ICESat-2 data with Argo data to investigate sea ice phytoplankton. Wanting to make their work as interoperable as possible, the development team created QUEST with a templated framework to enable easy addition of additional sensors and datasets. This presentation will showcase the QUEST module and the ways in which it can and is enabling oceanographic investigations.