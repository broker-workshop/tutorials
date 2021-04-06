# Fritz (SkyPortal + Kowalski)

[Fritz](https://github.com/fritz-marshal/fritz) is the science data platform for 
the [Zwicky Transient Facility (ZTF)](https://ztf.caltech.edu) Phase II.

It implements an end-to-end, scalable, API-first system for Time-domain Astronomy featuring:
- A multi-survey data archive and alert broker
- An interactive collaborative marshal for the transient, variable, and Solar system science cases, 
  with fine-grained access control
- A workhorse for machine learning applications and active learning
- Follow-up observation management with robotic and classical facilities

Fritz employs a modular architecture and
integrates and extends two major components: [Kowalski](https://github.com/dmitryduev/kowalski)
acts as the alert processor and data archive, and [SkyPortal](https://github.com/skyportal/skyportal),
which handles the rest of the stack.
The schematic overview of our system is shown below:

![img/fritz.png](https://docs.fritz.science/_images/fritz.jpg)

The project documentation is hosted at [https://docs.fritz.science](https://docs.fritz.science).

## A tour of Fritz from the user perspective

When you receive an invitation email to Fritz, click the link, which will take you to the Profile page.

![invite](https://user-images.githubusercontent.com/7557205/102668891-f8560a00-4141-11eb-9e98-e581bfccdff6.png)

Here, you can update your user preferences and create tokens to 
[interact with Fritz programmatically](https://docs.fritz.science/api.html).

![invited](https://user-images.githubusercontent.com/7557205/102669155-a95ca480-4142-11eb-98f5-cd0b595aa759.gif)

Now proceed to the Dashboard, the landing page. Here, you can rearrange and configure the widgets:

![landing](https://user-images.githubusercontent.com/7557205/102672731-803e1300-4146-11eb-8443-e1744a69afc0.gif)

New `Fritz` users are added to one or more groups. Users can also create groups themselves.
You can find the list of group members, the sources saved to
the group, and the group's [alert filters](https://docs.fritz.science/user_guide.html#alert-filters-in-fritz)
(defined on one of the alert streams that the group has access to)
on the Group page. Group admins can create new filters and modify existing ones.

Fritz provides powerful alert stream filtering capabilities. For a detailed discussion of those, refer to
the [alert filters](https://docs.fritz.science/user_guide.html#alert-filters-in-fritz) section of the docs.

Only system administrators can grant alert stream access to the groups.

![groups-1](https://user-images.githubusercontent.com/7557205/102672733-83d19a00-4146-11eb-99a9-2d1934e732d7.gif)

Users can also request admission to groups they are not a member of:

![groups-request-admission](https://user-images.githubusercontent.com/7557205/102672737-87652100-4146-11eb-98d4-6a98e2bd7004.gif)

On the Candidates page, the users can filter, scan and inspect the alerts that have passed filters of their groups
and save them to one or more groups. Candidates that are not saved to any group within 7 days are removed from Fritz.
Saved sources, on the other hand, are persisted forever.

![candidates-small](https://user-images.githubusercontent.com/7557205/102673044-6fda6800-4147-11eb-8d76-4d1d14b701f8.gif)

The Source page aggregates all kinds of information related to an object that exists on `Fritz`, such as photometry,
spectroscopy, auto-annotations, comments, finder charts, follow-up requests, and other data.

![source-small](https://user-images.githubusercontent.com/7557205/102673149-cba4f100-4147-11eb-9315-0cc2198830f6.gif)

![finder](https://user-images.githubusercontent.com/7557205/102672780-a95ea380-4146-11eb-9b2e-386b0d3d677e.gif)

Only the objects that have been posted to `Fritz`'s `SkyPortal` backend are saved in its database.
However, `Fritz`'s users can access the archive of ZTF alerts via the Alerts page:

![alerts](https://user-images.githubusercontent.com/7557205/102672770-a368c280-4146-11eb-9923-da4990897f69.gif)

