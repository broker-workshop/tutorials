# Fritz (SkyPortal + Kowalski)

- Table of Contents
  * [Introduction](#introduction)
  * [A quick tour of Fritz (from the user perspective)](#a-quick-tour-of-fritz--from-the-user-perspective-)
    + [User profile](#user-profile)
    + [Dashboard](#dashboard)
    + [Groups and filters](#groups-and-filters)
    + [Candidates and Sources](#candidates-and-sources)
    + [Mobile experience](#mobile-experience)
  * [Using the API](#using-the-api)

## Introduction

[Fritz](https://github.com/fritz-marshal/fritz) is the science data platform for 
the [Zwicky Transient Facility (ZTF)](https://ztf.caltech.edu) Phase II.

It implements an end-to-end, scalable, **API-first** system for Time-domain Astronomy featuring:
- A multi-survey data archive and alert broker
- An interactive, mobile-friendly collaborative platform for the transient, variable, and Solar system science cases, 
  with fine-grained access control
- A workhorse for machine learning applications
- Follow-up observation management with robotic and classical facilities

`Fritz` employs a modular architecture and
integrates and extends two major components: [Kowalski](https://github.com/dmitryduev/kowalski)
acts as the alert processor and data archive, and [SkyPortal](https://github.com/skyportal/skyportal),
which handles the rest of the stack.
The schematic overview of our system is shown below:

![img/fritz.png](https://docs.fritz.science/_images/fritz.jpg)

We note that while Fritz/SkyPortal/Kowalski can be centrally managed for a single-source of truth, 
it also can have many deployments with different configurations.

The project documentation is hosted at [https://docs.fritz.science](https://docs.fritz.science).

## A quick tour of Fritz (from the user perspective)

### User profile

Newly invited `Fritz` users are first taken to the Profile page.
Here, you can update your user info, preferences, and manage tokens to 
[interact with `Fritz` programmatically](https://docs.fritz.science/api.html). 
If you have a [gravatar](https://en.gravatar.com/) associated with your email address, 
it will be used as your avatar throughout the portal.

![lsst-ws-profile](https://user-images.githubusercontent.com/7557205/113780243-cb733600-96e3-11eb-8f9d-3ed5734da11b.gif)

System administrators can invite new users to `Fritz` and manage their roles/ACLs as well as group membership and 
alert stream access.

We use OAuth for user authentication.

### Dashboard

On the Dashboard, the landing page, the user can see a collection of configurable widgets displaying the
information on the most popular and latest saved sources, the newsfeed, conditions at telescopes and so on. 

![lsst-ws-dashboard](https://user-images.githubusercontent.com/7557205/113781577-c0b9a080-96e5-11eb-8f08-5dd59c00cf01.gif)

### Groups and filters

New `Fritz` users are added to one or more groups. 
Users can also create groups themselves and request admission to groups they are not a member of.
![lsst-ws-group-admission](https://user-images.githubusercontent.com/7557205/113792878-d20da780-96fb-11eb-8e5d-359bd0e34807.gif)

You can find and manage the list of group members, the sources saved to
the group, and the group's [alert filters](https://docs.fritz.science/user_guide.html#alert-filters-in-fritz)
(defined on one of the alert streams that the group has access to)
on the Group page. Group admins can create new filters (and modify existing ones).
System administrators can grant alert stream access to the groups.

`Fritz` provides rich alert stream filtering capabilities through its Kowalski backend, 
which consumes the ZTF Kafka alert stream, persisting the alerts to a database, 
and supplementing them with additional data such as the Galactic coordinates, 
external catalog cross-matches, machine learning scores etc.  

Alert filters are implemented as [aggregation pipelines](https://docs.mongodb.com/manual/core/aggregation-pipeline/) 
and are executed on the enhanced packets containing, in particular, the full photometry history. 
For a detailed discussion of this, refer to
the [alert filters](https://docs.fritz.science/user_guide.html#alert-filters-in-fritz) section of the docs.

Fritz performs automated checks of the filter definition such that no code audit is necessary. Valid changes are propagated
and applied almost immediately. Users can enable/disable filters, inspect filter version history, opt in for 
automatically saving passing objects to the filter's group and updating annotations each time an object passes the filter.

![lsst-ws-group-filter](https://user-images.githubusercontent.com/7557205/113790766-fe72f500-96f6-11eb-9c9a-7a7fc1ce8278.gif)

Filters can range from very simple that rely on, for example, 
[mostly ML scores](https://docs.fritz.science/user_guide.html#acai-hosted-filter), 
or implement [very complicated logic/computations](https://docs.fritz.science/user_guide.html#bts-rcf-program-full-filter). 
[Watch lists](https://docs.fritz.science/user_guide.html#watch-lists) can also be implemented as simple Fritz filters. 

Finally, we provide public alert databases for filter design and debugging.

### Candidates and Sources

Alerts passing a filter are posted to Fritz's SkyPortal backend as Candidates 
and appear on the Scanning page (for the corresponding filter groups).

Candidates do not have to originate from `Kowalski` and could be posted (manually) via the API. 

On the Candidates page, the users can filter, scan and inspect the objects that have passed filters of their groups
and save them to one or more groups. Candidates that are not saved to any group within 7 days are removed from `Fritz`.
Saved Candidates become Sources that are persisted indefinitely.

![lsst-ws-candidates2](https://user-images.githubusercontent.com/7557205/113795061-d8525280-9700-11eb-9ab0-69c599554ca5.gif)

Candidates/Sources can be supplemented with (structured) annotations, which is particularly useful for scanning:
![lsst-ws-candidates3](https://user-images.githubusercontent.com/7557205/113927408-4ac73f00-97a2-11eb-9a30-e2fc3c3c5e66.gif)

The Source page aggregates all kinds of information related to an object:
![lsst-ws-source-overview](https://user-images.githubusercontent.com/7557205/113828809-a7424400-9739-11eb-86b6-5eb525d32c82.gif)

In particular, users can generate finder charts and star lists for the source, check its observability, 
add and inspect redshift data:
![lsst-ws-source-1](https://user-images.githubusercontent.com/7557205/113828984-d658b580-9739-11eb-9272-d6e15928f894.gif)

Annotations provide a way to store structured data related to the source, 
while comments can be used to enter text/data in free-form. Sources can be classified according to a taxonomy 
(which users can post themselves provided they have the necessary ACL). 
Interactive offset plot displays positional information. 

Users can send notifications about the source to the group members, provided they opted in for receiving such.

![lsst-ws-source-2](https://user-images.githubusercontent.com/7557205/113928363-89a9c480-97a3-11eb-86d0-713b7c3ceee9.gif)

For sources [annotated with Gaia color information](https://skyportal.io/docs/advanced_usage.html?highlight=gaia), an 
HR diagram is rendered.
![lsst-ws-source-hr](https://user-images.githubusercontent.com/7557205/113984246-bab7e280-97ff-11eb-9c64-472af5864d64.gif)

Users can interactively inspect (optionally binned) photometry data in both magnitude and flux spaces.
Tools for data import, export, and granular access management are available.   
![lsst-ws-source-3](https://user-images.githubusercontent.com/7557205/113929129-882ccc00-97a4-11eb-86fb-e40fd7bb34ea.gif)

The photometry plot for sources annotated with period data additionally displays phase-folded light curves:
![lsst-ws-source-phase-fold](https://user-images.githubusercontent.com/7557205/113984100-922fe880-97ff-11eb-92e3-47a78aab0595.gif)

Tooling for extensive in-browser periodogram analysis for variable sources is also available:
![lsst-ws-source-periodogram-analysis](https://user-images.githubusercontent.com/7557205/113931157-00948c80-97a7-11eb-9125-1f7f9887718e.gif)

Users can inspect, analyze, and manage (including export/import) spectroscopy data.

We provide interfaces to work with both robotic and classical follow-up facilities, including 
a framework for dealing with allocations. 
In particular, we feature automated integration with the [SEDM](https://sites.astro.caltech.edu/sedm/). 

![lsst-ws-source-spectroscopy-follow-up](https://user-images.githubusercontent.com/7557205/113935794-ced1f480-97ab-11eb-8c48-ea60fb3aee03.gif)

Sources can be added to ("classical") observing runs:
![lsst-ws-source-observing-run](https://user-images.githubusercontent.com/7557205/113935874-ea3cff80-97ab-11eb-8705-7858febbe6d1.gif)

Sources can be added to favorites:
![lsst-ws-fav](https://user-images.githubusercontent.com/7557205/113828656-824dd100-9739-11eb-8291-2c7b779889a3.gif)

Users can query the objects that exist in Fritz's SkyPortal on the Sources page:
![lsst-ws-sources](https://user-images.githubusercontent.com/7557205/113937545-2bceaa00-97ae-11eb-9e66-1fe19aa79a62.gif)

Only the objects that have been posted to `Fritz`'s `SkyPortal` backend are saved in its database.
However, `Fritz`'s users can access the entire archive of ZTF alerts via the Alerts page:

![lsst-ws-alerts](https://user-images.githubusercontent.com/7557205/113936965-5d934100-97ad-11eb-9942-04c24bc6a2e0.gif)


### Mobile experience
Fritz's interfaces are mobile-friendly, so the app will work as expected on your phone or tablet:

<table>
<tr>
<td><img alt="phone" src="https://user-images.githubusercontent.com/7557205/114100592-5f2a3b00-9879-11eb-9dbc-9a4cdc46df77.gif" /></td>
<td><img alt="tablet" src="https://user-images.githubusercontent.com/7557205/114100803-b29c8900-9879-11eb-98c2-98ee91fd0e5e.gif" /></td>
</tr>
</table>


## Using the API

An API enables access to most of the underlying functionality of Fritz/SkyPortal/Kowalski. 
The workflows described above are all enabled by specific API calls.
The complete OpenAPI specification is available at [https://docs.fritz.science/api.html](https://docs.fritz.science/api.html).

To use the API, you will need a token that can be generated on the Profile page. Once you have that, you can
access Fritz programmatically as follows.

```python
import requests
from typing import Mapping, Optional
import urllib.parse

token = 'ea70a5f0-b321-43c6-96a1-b2de225e0339'

def api(
    method: str, 
    endpoint: str, 
    data: Optional[Mapping] = None, 
    base_url: str = "https://fritz.science",
):
    headers = {"Authorization": f"token {token}"}
    response = requests.request(
      method.upper(), 
      urllib.parse.urljoin(base_url, endpoint), 
      json=data, 
      headers=headers,
    )
    
    return response
```

For example, here is how to
[retrieve object's annotations](https://docs.fritz.science/api.html#tag/sources/paths/~1api~1sources~1obj_id~1annotations/get):

```python
object_id = "ZTF21aatjavc"

response = api("get", f"api/sources/{object_id}/annotations")
data = response.json().get("data", None)
print(data)
```

Which would yield something similar to: 

```python
[{
    "author_id": 3,
    "created_at": "2021-04-08T06:17:56.980090",
    "data": {
        "acai_b": 0.00011,
        "acai_h": 0.98887,
        "acai_n": 0.00361,
        "acai_o": 0.00925,
        "acai_v": 2e-05,
        "age": 0.0,
        "candid": "1558254455015015000",
        "n_det": 1,
    },
    "id": 916700,
    "modified": "2021-04-08T06:17:56.980090",
    "obj_id": "ZTF21aatjavc",
    "origin": "au-public:hosted",
}]
```
