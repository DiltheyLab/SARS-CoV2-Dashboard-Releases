# SARS-CoV2-Dashboard-Releases

Website for information on current SARS-CoV2 strains circulating in Duesseldorf

## Configuration

### Paths
The most important settings for your server are the paths to your data. You can set these up in the [default_settings.py](default_settings.py).
- `REF_FILE` path to the SARS-CoV2 reference genome
- `METADATA_FILE` path to metadata. See file formats for details.
- `FASTA_FOLDER` path to where you store your fasta files. The site detects files with `.fa` and `.fasta` suffix.
- `DISTANCE_MATRIX_FILE_STRICT` and `DISTANCE_MATRIX_FILE_LENIENT` path to distance matrices. For now only `DISTANCE_MATRIX_FILE_STRICT` is utilized (last updated: 11.02.2021). Point `DISTANCE_MATRIX_FILE_LENIENT` to the same file as `DISTANCE_MATRIX_FILE_STRICT` to be future proof. See file formats for details.
- `OUTPUT_FOLDER` this is where temporary fasta files for download of the current sequences are generated
- `CLONALITY_FILE` path to clonality. See file formats for details.

### File formats

#### Metadata
The metadata should be tab-separated and should have the following columns:
```
short_id    fasta_id    run_date    sample_date    nextstrain_clade    outbreak_id
```
If you provide a `*` as sample_date for a specific sample the sample will be unaffected by different choices for the selected date ranges and will be always visible. This can be utilized if you want to always depict the Wuhan reference for example. For samples which should not be grouped into specific outbreaks provide `-` as outbreak_id.


#### Distance Matrix
The distance matrix should be a serialized Python dictionary generated with [pickle](https://docs.python.org/3/library/pickle.html). The distance matrix should use the identifiers in the fasta files. The short id is only utilized for the visualization on the page. For each pair of sequences there should be two entries in dictionary, i.e. the matrix should contain symmetric entries for each pair. So for each pair of sequences (e.g.: Sequences A and B with fasta ids `seqA` and `seqB` having a distance of 3) the distance matrix should contain the entries: `distance_matrix["seqA","seqB"] = 3` and `distance_matrix["seqB","seqA"] = 3`. 

#### Clonality File
The clonality file should contain data in JSON format. It should be a [JSON object](https://www.w3resource.com/JSON/structures.php] (key/value pairs) with the keys being the dates in the format yyyy-mm-dd. Note that it might be useful to not only use samples of one specific date but rather to take samples into account that were in a sliding date range around a certain date. So the dates used as keys do not necessarily signify this specific date but rather the middle of the sliding window. 

The format of the values is exlained with the following example
```
"2020-12-02": {"p_d0": [0.00259007709415106, 0.007936507936507936, 0.0184233123359121], "p_d1": [0.008775495673989815, 0.01746031746031746, 0.031026180361708455], "same_d0": 5, "same_d1": 11, "total": 630},
```
For each date another Object is stored with the following entries:
- `p_d0` Fraction of pairs of samples with distance 0 between them. With a lower and upper bound. `[lower_bound fraction upper_bound]`
- `p_d1` Equivalent to `p_d0` with pairs of samples having distance 1
- `same_d0` Total number of pairs of samples having distance 0
- `same_d1` Equivalent to `same_d0` for pairs with distance 1
- `total` total number of pairs that went into this calculation

~                                                          
