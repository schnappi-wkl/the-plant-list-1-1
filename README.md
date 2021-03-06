# The Plant List version 1.1

Taxonomic names from The Plant List version 1.1.


## Missed names

Because there isn’t a single dump available on the web site I have to grab individual CSV files. Unfortunately many of these are “hidden” because they are not directly linked to form the genus browsing page. They are discovered when the GBIF darwin Core validator flags up a lack of referential integrity.

Can test for this locally:

```
SELECT CONCAT(“’”,`tpl1-1`.`AcceptedID`, “’,”) FROM `tpl1-1` LEFT JOIN `tpl1-1` AS t2 ON `tpl1-1`.AcceptedID = t2.ID WHERE `tpl1-1`.`AcceptedID` <> ‘’ AND t2.ID IS NULL;
```

There are still missing names, so to find these I compared my list with a dump provided by Markus Döring

```
SELECT DISTINCT CONCAT(“’”, tplmarkus.Genus, “’,”) FROM tplmarkus LEFT JOIN `tpl1-1` USING(ID) WHERE `tpl1-1`.Genus IS NULL;
```

I then grabbed those additional genus files from TPL.

## Mapping to publication identifiers

As of 2018-05-16 there were 1297759 taxa in the database, and the number of taxa with name or bibliographic identifiers were:

| identifier | count |
| ---|---:| 
| IPNI | 872848 |
| DOI | 103499 | 
| JSTOR | 22330 | 
| CiNii | 40 | 

## Matching to IPNI

Many IPNI records are not matched in TPL, can find matches to local IPNI by queries such as:

```
SELECT * FROM `tpl1-1` 
INNER JOIN `names` 
ON CONCAT(`tpl1-1`.Genus, ' ', `tpl1-1`.Species) = `names`.`Full_name_without_family_and_authors` 
WHERE `tpl1-1`.Infraspecific_rank = '' 
AND `names`.Infra_species = '' 
AND `names`.Id LIKE "%-1" 
AND `tpl1-1`.Publication = 'Telopea';
```
