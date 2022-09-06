# pytestの結果

## 実行プログラム

`EmApw.tgz`を任意のディレクトリにコピーし

```
tar -xvzf EmApw.tgz
```
で圧縮ファイルを解凍し

```
cd EmApw
```

でディレクトリに入り

```
make
```

`emapw.out`が作成されていたら成功です。


## Cu(100)のSCF計算


```
tar -xvzf Cu_001_7x7_scf_embpot.tgz
```

でファイルを解凍し、ディレクトリ内に入ります。
`qsub.sh`は

```
#!/bin/sh
#SBATCH -J  Cu_surface
#SBATCH -p  cmdinteractive
#SBATCH -N  1
#SBATCH -n  4

module load intel/2020.2.254
module load intelmpi/2020.2.254
#Above settings should be consistent with those used in the comparison.

#disable OPENMP parallelism
export OMP_NUM_THREADS=1
export I_MPI_ADJUST_ALLGATHERV=2
export I_MPI_PIN=1
cat $PE_HOSRFILE | awk '{ print$1":"$2/ENVIRON["OMP_NUM_THREADS"]}' >hostfile.$JOB_ID
mpirun -n 64  #!/bin/sh
#SBATCH -J  Cu_surface
#SBATCH -p  cmdinteractive
#SBATCH -N  1
#SBATCH -n  4

module load intel_compiler/2020.2.254
module load intelmpi/2020.2.254
#Above settings should be consistent with those used in the comparison.

#disable OPENMP parallelism
export OMP_NUM_THREADS=1
export I_MPI_ADJUST_ALLGATHERV=2
export I_MPI_PIN=1
cat $PE_HOSRFILE | awk '{ print$1":"$2/ENVIRON["OMP_NUM_THREADS"]}' >hostfile.$JOB_ID
mpirun -n 64  /home/〜/Embedding/EmApw/emapw.out
rm -f hostfile.$JOB_ID/EmApw/emapw.out
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