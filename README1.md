# vPhon: a Vietnamese phonetizer

Package: vPhon version 0.2.6

Author: James Kirby <j.kirby@ed.ac.uk>

Web: https://github.com/kirbyj/vPhon

This software takes UTF-8 Vietnamese orthography and returns broad transcription UTF-8 output in the International Phonetic Association (IPA) alphabet for three major dialects of Vietnamese: Northern (Hà Nội), Central (Huế), and Southern (Sài Gòn) speech.

For more information on the IPA, go to

https://www.internationalphoneticassociation.org/content/full-ipa-chart

Modified by Pham Van Dong to generate VI-X-SAMPA 

## Representational preliminaries

Note that an IPA representation can take one of two alternative starting points:

- a phonemic representation that clarifies minimal contrasts,
- a phonetic representation that reflects pronunciation details.

This implementation currently uses the first approach, meaning that many non-contrastive details are not represented. However, pronunciations where the citation forms differ from dialect to dialect are given, e.g. *anh* > [ɛŋ] (Northern), [an] (Southern).

### Length

Length is not explicitly represented excepting the phonemic length between the vowel pairs *â/ơ* and *ă/a*. The long variants are not marked with a diacritic; the short vowels *â* and *ă* are transcribed as [ɤ̆] and [ă], respectively. Orthographic *ô*, *ôô* are both transcribed as [o]; similarly, *o, oo* are both transcribed as [ɔ].

### Vowels

Segmental correspondences follow Thompson (1965: 98-103), Cao (1997: 126). vPhon assumes 11 nuclei (following Nguyễn 1998, cf. Cao 1998: 96-102) and 3 diphthongs /iə uə ɯə/; this decision was made to try and remain as agnostic as possible regarding the actual phonetic values of these segments. Monopthongization of labial-final diphthongs is represented for the Southern dialects (Thompson 1965: 86; Hoàng 1998: 174 *ff*.).

### Finals

By default, vPhon does not recognize final palatal segments [c ɲ], as their values may be predicted from the preceding vocalic segments.  However, the `-p` flag causes palatal [c ɲ] codas to be output (Hoàng 1989: 172 *ff*.; cf. Cao 1998: 88-102).

As of version 0.2.2, final labialized allophones of /ŋ k/ are represented as [ŋ͡m k͡p].

###Tones

vPhon represents tone using one of two methods. By default, vPhon will return Chao tone numbers based on Alves (2007a), Hoàng (1989), Nguyễn and Edmonson (1997), and Vũ (1982).

Name | North | Central | South
---- | ----- | ------- | -----
ngang|33|35|33
sắc|24/45|13/45|45
huyền|21|42|21
hỏi|312|312|214
ngã|35g|312|214
nặng|21/g|21g|212

vPhon also provides an option (given the `-6` flag) to return integer values for tones given in e.g. Phạm (2001, 59):

| Name  | Number |
| ----- | --- |
| ngang | 1   |
| huyền | 2   |
| ngã   | 3   |
| hỏi   | 4   |
| sắc   | 5   |
| nặng  | 6   |

If passed the `-8` flag, *sắc* and *nặng* tones in closed syllables are returned as 5b and 6b, respectively (Cao 1998; Michaud 2004; Phạm 2001).

Note that for the Central and Southern dialects, the relationship of tone to number is slightly different. Orthographic *hỏi* and *ngã*
tones are both phonetized as 4 when vPhon is passed the `-6` or `-8` flags, representing the (phonological) mergers present in those dialects (Hoàng 1989: 212 *ff.*)

## Installation

No installation is required. You must have a working version of Python (>= 2.4) installed and in your path. vPhon requires
the `__future__`, `string`, `StringIO`, and `optparse` modules, all of which should come standard with Python >= 2.4.x.

## Usage

vPhon takes an obligatory `-d, --dialect` option, specifying the dialect correspondence set to be used for phonetization
([N]orthern, [C]entral, or [S]outhern). The correspondence files may be found in the `Rules/` directory, and modified as necessary.

vPhon also takes, as an optional argument, a stream of UTF-8 text to be phonetized. If you have a file called `tuoi.txt`, for example, and want to create Southern-dialect IPA from it, either of the following will work:

```
> python vPhon.py -d S < tuoi.txt
> cat tuoi.txt | python vPhon.py --dialect Southern
```

Other optional flags can be seen by using the `-h, --help` flags:

```
[user@terminal]$ python vPhon.py -help
Usage: python vPhon.py <input> -d, --dialect N|C|S

Options:
  -h, --help            show this help message and exit
  -g, --glottal         prepend glottal stops to onsetless syllables
  -6, --pham            phonetize tones as 1-6
  -8, --cao             phonetize tones as 1-4 + 5, 5b, 6, 6b
  -p, --palatals        use word-final palatal velars in Northern dialects
  -t, --tokenize        preserve underscores or hyphens in tokenized input
  -m, --delimit         produce delimited output (e.g., an ninh = .a.n.33. .n.i.ɲ.33.)
  -d DIALECT, --dialect=DIALECT
                        specify dialect region ([N]orthern, [C]entral,
                        [S]outhern)
```

By default, output is sent to STDOUT.

If no argument is supplied on the command line, vPhon will enter an interactive mode allowing you to enter UTF-8 Vietnamese orthography on the command line. When you are done, send `EOF` (Ctrl-D) to get the output.

The `--tokenize` flag is useful if you are processing an older source in which morphemes are separated by hyphens, and you wish to retain the hyphens in your output, or if you are processing the output of e.g. [vnTokenizer](http://mim.hus.vnu.edu.vn/phuonglh/softwares/vnTokenizer):

```
[user@terminal]$ python vPhon.py -d N -t test/tokenized.txt 
căw24 oŋ͡m33_ta3 kuŋ͡m35g viən33 cɯə33 biət45
```

The `--delimit` flag will produce produce output where each phonetic symbol is separated by user-specified delimiter. If you use this flag, you must also specify a delimiter.

## Notes

All non-alphanumeric characters in the input are stripped prior to processing (unless the `--tokenize` option is selected, in which case `-` and `_` will be retained in the output). 

Any input containing non-Vietnamese orthography, or series of characters not conforming to Vietnamese phonotactics, will be braced in the output, e.g.

```
[These] [are] [not] [licit] [words] [20mi] [10-15km] [etc]
```

Try running the examples in the `test/` directory to get a better idea of this behavior.

## Citation

If you use vPhon for a project or paper, please cite it as:

    Kirby, James. 2008. vPhon: a Vietnamese phonetizer (version 0.2.6). Retrieved on <date> from http://github.com/kirbyj/vPhon/.
    
## Alternatives

[ADRPhone](http://www.mica.edu.vn/ADRPhone) is a lightweight, standalone phonetizer for Vietnamese written in standard C by Nguyễn Thị Minh Tuyền and Mathias Rossignol. It has many of the same functions as vPhon, but helpfully outputs XML as well. 

## Thank You

Thanks to Doug Cooper, Paul Sidwell, Marc Brunell, and Mark Alves for many useful comments and suggestions. Any errors or inconsistencies are, of course, mine alone, but I would love to hear about them.

## References

Alves, Mark J. (2007a). "A look at North-Central Vietnamese." In *SEALS XII*, ed. R. Wayland et al., Canberra, Australia.

Alves, Mark J. and Nguyễn Duy Hương. (2007b). "Notes on Thanh-Chương Vietnamese in Nghệ-an Province". In *SEALS VIII*, ed. M. Alves et al., Canberra, Australia, pp. 1-9.

Cao, Xuân Hạo. (1998). *Tiếng Việt – Mấy vấn đề ngữ âm, ngữ pháp và ngữ nghĩa*. Hà Nội: NXB Giáo dục.

Emeneau, M. B. (1951). *Studies in Vietnamese (Annamese) grammar*. University of California publications in linguistics (Vol. 8). Berkeley: University of California Press.

Hoàng, Thị Châu. (1989). *Tiếng Việt trên các miền đất nước: Phương ngữ học*. Hà Nội: Khoa học Xã hội.

Michaud, Alexis. (2004). Final consonants and glottalization: New perspectives from Hanoi Vietnamese. *Phonetica*, 61, 119-146.

Nguyễn, Đình-Hoà. (1997). *Vietnamese: Tiếng Việt không son phấn*. Amsterdam: John Benjamins Publishing Company.

Nguyễn, Văn Lợi & Edmondson, Jerold A. (1997). Tones and voice quality in modern northern Vietnamese: Instrumental case studies. *Mon-Khmer Studies*, 28, 1-18.

Phạm, Andrea. 2001. *Vietnamese tone: a new analysis*. University of Toronto dissertation. [Reprinted by Routledge, 2003.]

Thompson, Laurence E. (1965). *A Vietnamese reference grammar*. Seattle: University of Washington Press.

Vũ, T. P. (1982). Phonetic properties of Vietnamese tones across dialects. In *Papers in Southeast Asian Linguistics Volume 8 - Tonation*, ed. D. Bradley. Sydney, Australian National University, pp. 55-75.
