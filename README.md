# rosetta-2021_test

### This note documents the entire test process

下面提到的所有文件均可以在下面路径找到
```
/public3/home/pg3152/caojian/test2
```

![image](https://user-images.githubusercontent.com/64938817/175912369-20b7d69c-e85c-4c4e-9955-fd3b650ef4c0.png)


## 1.构建motif文件

```
H 5 20, L 1 3, E 3 10, L 1 3, H 5 20
```

## 2.pdbs文件路径
这里所用的PDB文件为下载的400多个Aifold预测的PDB结构数据文件路径为:

```
/public3/home/pg3152/caojian/ai_test/pdbs_ai.txt
```

![image](https://user-images.githubusercontent.com/64938817/175904415-b50ce469-0dda-4eac-8198-500997842db3.png)

## 3.生成segments文件

```
#!/bin/bash
/public3/home/pg3152/zzl/zzl_softwares/rosetta_src_2021.16.61629_bundle/main/source/bin/segment_file_generator.linuxgccrelease \
-ignore_unrecognized_res \
-pdb_list_file pdbs_ai.txt \
-motif_file motif-e.txt \
-strict_dssp_changes false
```
## 4.RosettaScript文件的书写
```
<ROSETTASCRIPTS>
	<SCOREFXNS>
	</SCOREFXNS>
	<RESIDUE_SELECTORS>
	</RESIDUE_SELECTORS>
	<FILTERS>
	</FILTERS>
	<MOVERS>
	<!--main mover-->
		<AssemblyMover
        	 name="assemble"
        	 minimum_cycles="10000"
        	 maximum_cycles="100000"
        	 start_temperature="0.6"
        	 end_temperature="0.6"
        	 hashed="false"
          	 window_width="4"
         	 model_file_name="smotifs_H_5_20_L_1_3_E_3_10_L_1_3_H_5_20.segments" >
	<!--scoring control-->
			<AssemblyScorers>
				<MotifScorer weight="1" />
				<InterModelMotifScorer weight="10" />
			</AssemblyScorers>
	<!--requirements control-->
			<AssemblyRequirements>
				<ClashRequirement maximum_clashes_allowed="0" clash_radius="5" />
				<SizeInSegmentsRequirement minimum_size="5" maximum_size="7" />
				<DsspSpecificLengthRequirement dssp_code="H" minimum_length="12" maximum_length="1000" />
			</AssemblyRequirements>
		</AssemblyMover>
	</MOVERS>
	<APPLY_TO_POSE>
	</APPLY_TO_POSE>
	<PROTOCOLS>
		<Add mover_name="assemble" />
	</PROTOCOLS>
</ROSETTASCRIPTS>

```
## 5.flag文件编写
```
-ignore_unrecognized_res
-detect_disulf false
-mh
    -score
        -use_ss1 true
        -use_ss2 true
        -use_aa1 false
        -use_aa2 false
    -path
        -motifs /public3/home/pg3152/zzl/zzl_softwares/rosetta_src_2021.16.61629_bundle/main/database/additional_protocol_data/sewing/xsmax_bb_ss_AILV_resl0.8_msc0.3/xsmax_bb_ss_AILV_resl0.8_msc0.3.rpm.bin.gz
        -scores_BB_BB /public3/home/pg3152/zzl/zzl_softwares/rosetta_src_2021.16.61629_bundle/main/database/additional_protocol_data/sewing/xsmax_bb_ss_AILV_resl0.8_msc0.3/xsmax_bb_ss_AILV_resl0.8_msc0.3

```
## 6.运行sewing组装

```
#!/bin/bash
/public3/home/pg3152/zzl/zzl_softwares/rosetta_src_2021.16.61629_bundle/main/source/bin/rosetta_scripts.linuxgccrelease \
-s AF-A0A0D2F0A9-F1-model_v2.pdb \
-parser:protocol RosettaScript.xml @flag \
-nstruct 10  \
-out:path:pdb output
```


