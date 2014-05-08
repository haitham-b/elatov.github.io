---
title: VCAP5-DCD Objective 1.3 – Determine Risks, Constraints, and Assumptions
author: Karim Elatov
layout: post
permalink: /2012/08/vcap5-dcd-objective-1-3-determine-risks-constraints-and-assumptions/
dsq_thread_id:
  - 1406850360
categories:
  - VCAP5-DCD
  - VMware
tags:
  - VCAP5-DCD
---
### Differentiate between the general concepts of a risk, a requirement, a constraint, and an assumption.

From the <a href="http://www.slideshare.net/ProfessionalVMware/professionalvmware-brownbag-jason-boche-vcapdcd-objective-1" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.slideshare.net/ProfessionalVMware/professionalvmware-brownbag-jason-boche-vcapdcd-objective-1']);">US BrownBag Objective 1</a> Slide Deck:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/08/arch-vision.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/08/arch-vision.png']);"><img class="alignnone size-full wp-image-2720" title="arch-vision" src="http://virtuallyhyper.com/wp-content/uploads/2012/08/arch-vision.png" alt="arch vision VCAP5 DCD Objective 1.3 – Determine Risks, Constraints, and Assumptions " width="1111" height="691" /></a>

and from the <a href="http://portal.sliderocket.com/BLIHZ/VCAP5-DCD-BrownBag---Session-1" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://portal.sliderocket.com/BLIHZ/VCAP5-DCD-BrownBag---Session-1']);">APAC BrownBag Session 1 Slide Deck</a>:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/08/rcar.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/08/rcar.png']);"><img class="alignnone size-full wp-image-2721" title="rcar" src="http://virtuallyhyper.com/wp-content/uploads/2012/08/rcar.png" alt="rcar VCAP5 DCD Objective 1.3 – Determine Risks, Constraints, and Assumptions " width="1240" height="569" /></a>

### Given a statement, determine whether it is a risk, requirement, a constraint, or an assumption

From this <a href="http://virtuallyhyper.com/wp-content/uploads/2013/04/vcap-dcd_notes.pdf" onclick="javascript:_gaq.push(['_trackEvent','download','http://virtuallyhyper.com/wp-content/uploads/2013/04/vcap-dcd_notes.pdf']);">PDF</a>:

> **Architectural Vision: A high level vision for the project** includes the following aspects:
> 
> 1.  Scope &#8211; Identify Scope in detail 
>     *   Defined scope prevents unintended expansion (ie this project includes the US productions servers only. No dev/test and no Canada servers)
>     *   prevents the need to renegotiate cost of project or work for fee
> 2.  Goals &#8211; set specific goals for a project 
>     *   Goals need to be specific, measurable, and actionable
>     *   without goals it is difficult to determine success and value of a project
>     *   i.e the organization wants to achieve a 50% reduction in production server equipment by the end of the year
> 3.  Requirements &#8211; Identify key business and technical requirements for the project 
>     *   ie, SOX compliance, physical separation from production and dev/test, up-time requirements, etc
> 4.  Assumptions- Design components that are assumed valid without proof 
>     *   i.e. the organization has sufficient bandwidth between sites for replication
> 5.  Constrains &#8211; constraints limit the design choices, could be a policy, process, or technical constraint 
>     *   i.e. due to existing relationships all hardware is dell
> 6.  Risks &#8211; Identify risks that might prevent achieving project goals 
>     *   i.e. lack of core redundancy introduces risk of %99.99 up-time
>     *   discussing risks eliminated surprise 

Also good examples can be found here:

*   http://www.virten.net/2012/06/vdcd510-objective-1-3-determine-risks-constraints-and-assumptions/
*   http://www.slideshare.net/ProfessionalVMware/professionalvmware-brownbag-jason-boche-vcapdcd-objective-1

### Analyze impact of VMware best practices to identified risks, constraints, and assumptions

From the <a href="http://www.slideshare.net/ProfessionalVMware/professionalvmware-brownbag-jason-boche-vcapdcd-objective-1" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://www.slideshare.net/ProfessionalVMware/professionalvmware-brownbag-jason-boche-vcapdcd-objective-1']);">US BrownBag Objective 1</a> Slide Deck:

<a href="http://virtuallyhyper.com/wp-content/uploads/2012/08/best-practices-rules.png" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://virtuallyhyper.com/wp-content/uploads/2012/08/best-practices-rules.png']);"><img class="alignnone size-full wp-image-2726" title="best-practices-rules" src="http://virtuallyhyper.com/wp-content/uploads/2012/08/best-practices-rules.png" alt="best practices rules VCAP5 DCD Objective 1.3 – Determine Risks, Constraints, and Assumptions " width="1074" height="541" /></a>

Basically try to be flexible with best practices, not everything falls under best practices or a certain criteria. For this section the Blue Print has a link to an excellent document from Glasshouse, I would definitely recommend reading it: <a href="http://communities.vmware.com/docs/DOC-17431" onclick="javascript:_gaq.push(['_trackEvent','outbound-article','http://communities.vmware.com/docs/DOC-17431']);">Developing Your Virtualization Strategy and Deployment Plan</a>

<p class="wp-flattr-button">
  <a class="FlattrButton" style="display:none;" href="http://virtuallyhyper.com/2012/08/vcap5-dcd-objective-1-3-determine-risks-constraints-and-assumptions/" title=" VCAP5-DCD Objective 1.3 – Determine Risks, Constraints, and Assumptions" rev="flattr;uid:virtuallyhyper;language:en_GB;category:text;tags:VCAP5-DCD,blog;button:compact;">Differentiate between the general concepts of a risk, a requirement, a constraint, and an assumption. From the US BrownBag Objective 1 Slide Deck: and from the APAC BrownBag Session 1...</a>
</p>