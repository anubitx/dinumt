


getInput( \%infile_hash, \%readgroup_hash, \%mask_hash, \%mt_hash );

`infile_hash` is used to store alignments.  
`readgroup_hash` is used for processing alignments of user defined read groups only. LOW PRIORITY.  
`mask_hash` is chr, start as keys and end as values for predefined numts from ReferenceNumts.bed.   
`mt_hash` stores various different ways in Mt sequence is defined in CRAM files.   

Lines 395-427: Commands to read in the input SAM/BAM/CRAM with chrM as the region and store commands in `@input_lines`.   
Lines 430-454: Commands to read in the input SAM/BAM/CRAM for reads overlapping `ReferenceNumts.bed` regions and add them to `@input_lines`.   
Read input bam files to select reads overlapping `mitochondria` or `ReferenceNumts.bed` with following conditions met.   

1. skip reads if mate is unmapped or mapped to the same chromosome `if ( $rnext eq '=' || $rnext eq '*' ) { next; }`

```ERR1019082.832954309	163	chrM	1	54	29S71M	=	231	334	```

2. skip reads if NOT mapped to Mt but mate is mapped to the Mt `if ( $rname !~ /M/ && $rnext =~ /M/ ) { next; }` ???   

Alternately, retain reads mapped to the Mt and mate mapped to any other reference sequence as long as the mate doesn't overlap masked region.

Lines 478-486: dnext = 1 and dir = 1 if mate or the read is on reverse strand else set to 0.  
Lines 488-507: Skip reads if their mates also overlap the masked region ??   
Lines 509-529: Get the alignment of the mate.  
Lines 535-549: Store information of alignments.  

            $$infile_hash{$seq_num}{group} = 0;
            $$infile_hash{$seq_num}{seq}   = $seq;

            $$infile_hash{$seq_num}{dir}   = $dir;
            $$infile_hash{$seq_num}{qname} = $qname;
            $$infile_hash{$seq_num}{rname} = $rname;
            $$infile_hash{$seq_num}{pos}   = $pos;
            $$infile_hash{$seq_num}{cigar} = $cigar;
            $$infile_hash{$seq_num}{cnext} = $cnext;
            $$infile_hash{$seq_num}{rnext} = $rnext;
            $$infile_hash{$seq_num}{pnext} = $pnext;
            $$infile_hash{$seq_num}{dnext} = $dnext;
            $$infile_hash{$seq_num}{tlen}  = $tlen;
            $$infile_hash{$seq_num}{qual}  = $qual;
            $$infile_hash{$seq_num}{line}  = $line1;





seqCluster( \%infile_hash );
linkCluster( \%infile_hash );
mapCluster( \%infile_hash, \%outfile_hash, \%readgroup_hash );
findBreakpoint( \%outfile_hash, \%readgroup_hash, \%mask_hash );
scoreData( \%outfile_hash );
report( \%outfile_hash );
