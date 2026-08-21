### 📋 Sanger Sequencing Merge, QC & BLAST Pipeline Guide

Welcome to the automated Sanger sequence merging and alignment pipeline! This notebook takes your raw `.seq` or `.ab1` chromatogram files, trims low-quality bases, merges paired reads, trims synthetic primers, and runs a remote NCBI BLAST search to identify viruses, organisms, and potential co-infections.

#### **⚙️ Pipeline Architecture**

```text
       [ Raw .seq or .ab1 Files ]
                   │
                   ▼
       [ Step 1: Quality Control ] ───── ( Trim N's & Phred < 15 )
                   │
                   ▼
       [ Step 2: Primer Trimming ] ───── ( Remove Custom Primers )
                   │
                   ▼
       [ Step 3: Read Merging ] ──────── ( 1-way, 2-way, or N-way )
                   │
                   ├──────────────────────────────────┐
                   ▼                                  ▼
      [ Step 4: Quality Metrics ]        [ Step 5: Remote NCBI BLASTn ]
       ( Mean Q, Q20%, Q30% )          ( Taxonomy & Viral Co-infections )
                   │                                  │
                   └─────────────────┬────────────────┘
                                     ▼
                        [ Step 6: Final Reporting ]
                     ( SUMMARY.csv & merged_sequences )
                                     │
                                     ▼
                      [ Step 7: Visual Verification ]
                        ( In-Notebook Alignments )




```

#### **1. Prepare Your Files**
Gather your sequencing files. The pipeline supports both standard `.seq`/`.fasta` files and raw **`.ab1` chromatogram files**. (Using `.ab1` files is highly recommended, as the script will use the actual Phred quality scores to make smarter merging decisions and generate QC metrics).
*   **Tip:** For the fastest upload, compress all your files into a single `.zip` archive.

#### **2. How to Run the Pipeline**
Run the code cells below in order by clicking the **Play (▶)** button on the left side of each cell.

*   **Cell 1: Setup & Upload**
    Check the **`clear_previous_uploads`** box if you are starting a new batch (this prevents old files from showing up in your dropdown menus). Click play, then select your files to upload.
*   **Cell 1.5: Interactive File Grouper**
    Click play to launch the GUI. Type a Sample ID in the text box, then use the dropdown menus to match your Forward and Reverse reads for that sample. When you are done, click the green **Save to merge.csv** button.
*   **Cell 2: Load Core Functions**
    Click play to load the background sequence processing, QC tracking, and BLAST tools into memory.
*   **Cell 3: Configure & Run**
    Before clicking play, set your parameters on the right side of the cell:
    *   `min_overlap`: Minimum overlapping base pairs required to merge reads (default 40).
    *   `Custom Primers`: Enter the **Name** (which *must* exist inside your filename, e.g., `NP1F`) and the **Sequence** of up to 4 primers to automatically trim them. Leave blank to skip trimming.
    
    Once configured, click play. The pipeline will merge your reads, calculate Q-scores, search the NCBI database, and automatically download a `.zip` file with your results.
*   **Cell 4: View Q-Scores & NCBI Alignments**
    Click play to instantly view your Sample Q-Scores, top BLAST hits, and a visual nucleotide alignment (mimicking the NCBI website) directly inside the notebook.

#### **3. Understanding Your Outputs**
Inside your downloaded `sanger_pipeline_output.zip`, you will find:
*   `SUMMARY.csv`: Your primary report. Includes Sample ID, Merge Type, Quality Metrics (Mean Q, Q20%, Q30%), the top BLAST hit, and the final sequence.
*   `blast_best_hits.tsv`: The top scoring hit for *each unique organism* found per sample. (Ideal for spotting viral co-infections).
*   `blast_results.tsv`: The complete, unfiltered list of all BLAST hits.
*   `merge_summary.tsv`: A highly detailed technical breakdown of lengths, overlaps, mismatches, and merge success.
*   `merged_sequences.fasta`: Your final sequences in standard FASTA format, ready for downstream use.
*   `failed_samples.tsv` *(if applicable)*: A log of any samples that failed to merge and why.
