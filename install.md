Qiitaに記載されているまま実行します。

libxcのインストールまでは問題ありませんでした。

---

### GPAWのインストール

---

```
$ wget https://pypi.org/packages/source/g/gpaw/gpaw-21.1.0.tar.gz
$ tar xf gpaw-21.1.0.tar.gz
$ cd gpaw-21.1.0
```
```
$ vi siteconfig.py
```
ここで`siteconfig.py`はグループ番号と個人IDだけ変更しています。

```
$ python setup.py build
$ python setup.py install
```
インストールが完了し、`gpaw info`を実行したところ、slurmファイルに正しく出力されましたが、
```
 ------------------------------------------------------------------------------------------------------------------
| python-3.8.12     /home/k0227/k022707/ASE/ase/bin/python                                                         |
| gpaw-21.1.0       /home/k0227/k022707/ASE/ase/lib64/python3.8/site-packages/gpaw/                                |
| ase-3.22.1        /home/k0227/k022707/ASE/ase/lib64/python3.8/site-packages/ase/                                 |
| numpy-1.23.1      /home/k0227/k022707/ASE/ase/lib64/python3.8/site-packages/numpy/                               |
| scipy-1.9.0       /home/k0227/k022707/ASE/ase/lib64/python3.8/site-packages/scipy/                               |
| libxc-5.2.3       yes                                                                                            |
| _gpaw             /home/k0227/k022707/ASE/ase/lib64/python3.8/site-packages/_gpaw.cpython-38-x86_64-linux-gnu.so |
| MPI enabled       yes                                                                                            |
| OpenMP enabled    yes                                                                                            |
| scalapack         yes                                                                                            |
| Elpa              no                                                                                             |
| FFTW              yes                                                                                            |
| libvdwxc          no                                                                                             |
| PAW-datasets (1)  /home/k0227/k022707/ASE/gpaw-setups-0.9.20000                                                  |
 ------------------------------------------------------------------------------------------------------------------
 ```
`OpenMP enabled    yes`となります。

続いて`pytest`を実行したところ、ほとんどPASSEDとなり、ちらほらSKIIPEDが見られましたがそのほかに少しだけFAILEDが発生しました。
```
gpaw/test/test_electronphonon.py::test_electronphonon FAILED
gpaw/test/exx/test_derivs.py::test_exx_derivs XFAIL
gpaw/test/lrtddft/test_placzek_profeta_albrecht.py::test_lrtddft_placzek_profeta_albrecht FAILED
gpaw/test/response/test_iron_sf_gssALDA.py::test_response_iron_sf_gssALDA FAILED
gpaw/test/response/test_silicon_chi_RPA.py::test_response_silicon_chi_RPA FAILED
```

気にせず`run.sh`を実行したところ
```
/home/k0227/k022707/ASE/ase/lib64/python3.8/site-packages/ase/utils/__init__.py:62: FutureWarning: convert_string_to_fd does not facilitate proper resource management.  Please use e.g. ase.utils.IOContext class instead.
  warnings.warn(warning)
Energy: -6.631 eV
Forces: -0.639 eV/Angstrom
```
slurmファイルにはこのように表示されます。
結果も出力されているので問題はなさそうに思います。