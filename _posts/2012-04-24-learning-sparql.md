---
layout: post
title: Learning SPARQL
permalink: /2012/04/learning-sparql/
---

In my [post about the future of data.lincoln.ac.uk](http://httpster.org/the-future-of-data-lincoln-ac-uk/) I committed us to providing a [SPARQL](http://en.wikipedia.org/wiki/Sparql) endpoint for University of Lincoln open linked data. Today I've spent an insane amount of time learning how on earth SPARQL works.

I set up a local triplestore on my laptop using [ARC2](https://github.com/semsol/arc2/), I used [EasyRDF](https://github.com/njh/easyrdf) to output a dump of the [staff directory](http://phone.online.lincoln.ac.uk/) as RDF XML, I used Chris Gutteridge's [Graphite](http://graphite.ecs.soton.ac.uk/) and [Browser](http://graphite.ecs.soton.ac.uk/browser/) code to check the EasyRDF output looked right, then finally after I'd loaded the data into the triplestore I used Dave Challis' [SPARQLfront](https://github.com/davechallis/SPARQLfront) to query the data.

So far I've managed to work out the following queries:

(copied from [https://gist.github.com/2475211/](https://gist.github.com/2475211/))

---

**Get all divisions**

	PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
	PREFIX org: <http://www.w3.org/ns/org#>

	SELECT DISTINCT ?dept ?deptName
	WHERE {
	  ?dept a org:OrganizationalUnit .
	  ?dept rdfs:label ?deptName .
	  ?dept org:unitOf <http://lincoln.ac.uk/> .
	}

**Get all departments in $DIVISION$**

	PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
	PREFIX org: <http://www.w3.org/ns/org#>

	SELECT DISTINCT ?dept ?deptName
	WHERE {
	  ?dept a org:OrganizationalUnit .
	  ?dept rdfs:label ?deptName .
	  ?dept org:unitOf <http://id.online.lincoln.ac.uk/division/$DIVISION$> .
	}

**Get all staff in $DIVISION$**

	PREFIX foaf: <http://xmlns.com/foaf/0.1/>
	PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
	PREFIX org: <http://www.w3.org/ns/org#>

	SELECT  ?givenName ?familyName
	WHERE {
	  ?person a foaf:Person .
	  ?person org:memberOf <http://id.online.lincoln.ac.uk/division/$DIVISION$> .
	  ?person org:memberOf ?division .
	  ?division a org:OrganizationalUnit .
	  ?division org:unitOf ?parent .
	  ?person foaf:givenName ?givenName .
	  ?person foaf:familyName ?familyName .
	  FILTER (?parent = "http://lincoln.ac.uk/")
	}

**Get all staff in $DEPARTMENT$**

	PREFIX foaf: <http://xmlns.com/foaf/0.1/>
	PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
	PREFIX org: <http://www.w3.org/ns/org#>

	SELECT ?givenName ?familyName
	WHERE {
	  ?person a foaf:Person .
	  ?person org:memberOf <http://id.online.lincoln.ac.uk/department/$DEPARTMENT$> .
	  ?person org:memberOf ?division .
	  ?division a org:OrganizationalUnit .
	  ?division org:unitOf ?parent .
	  ?person foaf:givenName ?givenName .
	  ?person foaf:familyName ?familyName .
	  FILTER (?parent != "http://lincoln.ac.uk/")
	}

**Get the division that a member of staff is in**

	PREFIX foaf: <http://xmlns.com/foaf/0.1/>
	PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
	PREFIX org: <http://www.w3.org/ns/org#>

	SELECT ?name
	WHERE {
	  ?division a org:OrganizationalUnit .
	  ?division org:unitOf <http://lincoln.ac.uk/> .
	  ?division rdfs:label ?name .
	  ?division org:hasMember <http://id.online.lincoln.ac.uk/person/$ID$> .
	}

**Get the department that a member of staff in is**

	PREFIX foaf: <http://xmlns.com/foaf/0.1/>
	PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
	PREFIX org: <http://www.w3.org/ns/org#>

	SELECT ?name
	WHERE {
	  ?division a org:OrganizationalUnit .
	  ?division org:unitOf ?parent .
	  ?division rdfs:label ?name .
	  ?division org:hasMember <http://id.online.lincoln.ac.uk/person/$ID$> .
	  FILTER (?parent != "http://lincoln.ac.uk/")
	}

---

So that is what I've worked out so far.

Once I've got the final spaces data in the next week or so I'll figure out some more queries which mix people and spaces.

Some of the resources that I found useful whilst learning SPARQL include:

* [http://jena.sourceforge.net/ARQ/Tutorial/](http://jena.sourceforge.net/ARQ/Tutorial/)
* [http://www.pezholio.co.uk/2011/01/a-beginners-guide-to-sparqling-linked-data-part-1/](http://www.pezholio.co.uk/2011/01/a-beginners-guide-to-sparqling-linked-data-part-1/)
* Checking out the SPARQL queries that are executed when rendering any of the [data.southampton.ac.uk pages](http://data.southampton.ac.uk/) (click the "This page was constructed using X queries to the SPARQL Endpoint." link at the bottom of each page)

My thanks also go to [Chris Gutteridge](http://www.ecs.soton.ac.uk/people/cjg), [Tony Hirst](http://ouseful.info), [Dave Challis](https://twitter.com/#!/davechallis), [Nicholas J Humfrey](http://www.aelius.com/njh/), and [Benjamin Nowack](https://github.com/semsol) for their various blog posts and code libraries which I also found useful.
