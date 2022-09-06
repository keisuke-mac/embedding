# Embedding法

## 実行プログラム

`EmApw.tgz`を任意のディレクトリにコピーします。次にファイルを解凍をします。

```
tar -xvzf EmApw.tgz
```
解凍できたら

```
cd EmApw
```

でディレクトリに入りmakeします。

```
make
```

カレントディレクトリ内に`emapw.out`が作成されていたら成功です。


## Cu(100)のSCF計算


```
tar -xvzf Cu_100_7x7_scf_embpot.tgz
```

でファイルを解凍し、ディレクトリ内に入ります。

- input.dat
- param.dat
- d3ps-pot.dat（FCC Cu 原子球外側の領域のポテンシャル）
- g_emb.dat（表面領域とバルクとの境界面の構造データ）
- orb.dat（DOSを射影する軌道のデータ：s, px, py, pz・・・・）
> 
があることをご確認ください。さらにサブfolderの./e_pot_scf中に`00.vemb.dat`があることを確認してください。


<br>
続いて計算です。job scriptは

```
#!/bin/sh
#SBATCH -J  Cu_surface
#SBATCH -p  cmdinteractive
#SBATCH -N  1
#SBATCH -n  64

module load intel_compiler/2020.2.254
module load intel_mpi/2020.2.254
#Above settings should be consistent with those used in the comparison.

cat $PE_HOSRFILE | awk '{ print$1":"$2/ENVIRON["OMP_NUM_THREADS"]}' >hostfile.$JOB_ID
srun  /home/t0102/t010200/Embedding/EmApw/emapw.out
rm -f hostfile.$JOB_ID
```

です。ただし`emapw.out`のパスはご自身のものを指定してください。またコンパイラもご自身の環境により適宜変更してください。計算の収束状況は
```
grep "jte, rms" 00.output
```
により確認できます。rmsは入力電子密度と出力電子密度の差をユニットセルで平均したものです（Hartree原子単位）。
<br>
計算が終われば
```
grep evcm 00.output
```
により真空準位を確認することができます。今回のCuのフェルミエネルギー$\mathrm{E}_F$は$0.325\, \mathrm{Hartree}$なので仕事関数は$(\mathrm{evcm}-0.325)\times 27.211386\,eV$となります。