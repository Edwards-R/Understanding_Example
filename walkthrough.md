# A practical demonstration of Understandings

## Criteria for success

In order to do any assessment we must first defined the measurement of success. This one is quite simple:

*Every possible name must be resolved to the current type, without manual intervention. In situations where this is not possible, this must be clearly denoted*

>Please note that, as this demonstration is working on a snapshot of the BWARS database, the *actual* taxonomy & nomenclature referred to here can only be as modern as the snapshot. This will *not* matter at all for this demonstration and will only affect those who already know *too much* about GB Aculeate Hymenoptera taxonomy!

## Method

First of all we will bypass the Understanding system to see how the data look under the current system. We will then assess this under our criteria for success.

Secondly, we will re-enable the understanding system and run the same test.

After this, we will compare and contrast how the two systems performed.

## Walkthrough

>This walkthrough will not include full results, only summary figures. This is done because the 'full' results can span a few thousand lines! Summary figures will be reported e.g. '293 results', but for full details you will need to follow along yourself.

## Non-understanding system

Select the count of all valid records of 'Bombus lucorum':

```
SELECT count(c.id)
FROM core c
JOIN species s on c.nik = s.id
JOIN genus g on s.parent = g.id
JOIN check_checker cc on c.id = cc.core_id
WHERE g.name like 'Bombus'
AND s.name like 'lucorum'
AND cc.is_suspended=0
```

> Result: 13802

This means that there are 13802 records stored as having been *entered into the database* as 'Bombus lucorum'.

*Bombus lucorum* as a taxon was split in GB in 2008, due to the findings of Murray et al. The study can be found at:

>https://www.researchgate.net/profile/Mark-Brown-32/publication/226718911_Cryptic_species_diversity_in_a_widespread_bumble_bee_complex_revealed_using_mitochondrial_DNA_RFLPs/links/09e41507d79ad7aaf9000000/Cryptic-species-diversity-in-a-widespread-bumble-bee-complex-revealed-using-mitochondrial-DNA-RFLPs.pdf

The study has been widely supported and confirmed, with recognition of this split across Europe.

In response to this split, BWARS created an input system which attempts to separate 'true' *Bombus lucorum* records from *Bombus lucorum s.l.*. This system predates the Understandings system, which removes 's.l.' from use and replaces it with 'agg' - short for 'aggregate'.

We can simulate the lack of this ***manual*** record correction process by incorporating the records of 'Bombus lucorum agg' into our search:

```
SELECT count(c.id)
FROM core c
JOIN species s on c.nik = s.id
JOIN genus g on s.parent = g.id
JOIN check_checker cc on c.id = cc.core_id
WHERE g.name like 'Bombus'
AND s.name like 'lucorum%'
AND cc.is_suspended=0
```

> Result: 27759

This means that there are `27759 - 13802 = 13957` records of 'Bombus lucorum agg'. We can check that by only selecting records of 'Bombus lucorum agg':

```
SELECT count(c.id)
FROM core c
JOIN species s on c.nik = s.id
JOIN genus g on s.parent = g.id
JOIN check_checker cc on c.id = cc.core_id
WHERE g.name like 'Bombus'
AND s.name like 'lucorum agg'
AND cc.is_suspended=0
```

> Result: 13957

### Conclusion

We have two figures we could accept from this section. The more conservative number is `13802` - the number of records stored as *Bombus lucorum*. The less conservative figure, which represents a *total* lack of data verification, is `27759`. This number is, in my opinion, closer to what the vast majoriy of non-understanding systems would produce.

## Understanding system

A much simpler query this time. We are only interested in data which corresponds to the current understanding of 'Bombus lucorum', which at the time of writing is `Bombus lucorum: iso. Murray et al: 2008`.

This is done with the following query, which first of all uses the `NoNomS` system to resolve the provided understanding to its current interpretation - this is what `s2` is doing. We're then querying `g2` and `s2` because we want to know the current understanding rather than the input understanding.

```
SELECT count(c.id)
FROM core c
JOIN species s on c.nik = s.id
JOIN species s2 on s.current = s2.id
JOIN genus g2 on s2.parent = g2.id
JOIN check_checker cc on c.id = cc.core_id
WHERE g2.name like 'Bombus'
AND s2.name like 'lucorum'
AND s2.author like 'Murray et al'
AND s2.year = 2008
AND cc.is_suspended=0
```

> Result: 114

### Conclusion

We have only one figure from this section: `114`.

## Conclusion

Taking even the conservative number from the non-understanding section, `13802`, this is still vastly different to the understanding result of `114`.

With this information, which result is more accurate? The answer is, by far, `114`. The only currently accepted method of determining a specimen to be Bombus lucorum since the 2008 split has been either genetic or chemical analysis. If anything, the figure of 114 is an over-estimation caused by some data being submitted under an incorrect understanding. There most ***certainly*** haven't been even 500 specimens from GB assessed using genetic or chemical analysis. Even accounting for allowing record duplication running at 30% - which is a very low number for an aggregating biological data scheme - the idea of ~10,000 genetically/chemically analysed records is utter nonsense.

---

In conclusion, the Understandings system, implemented via NoNomS (**No**rmalised **Nom**enclature **S**ystem), has vastly improved the quality of BWARS's stored data.