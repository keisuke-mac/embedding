# pytestの結果

濱本先生の手法通りに

`gpaw/basis_data.py`に

```
        import matplotlib as mpl
        mpl.use('Agg')
```

を追加し、下の`pytest.sh`を実行しましたが、

```
#!/bin/sh
#SBATCH -J pytest
#SBATCH -p F1cpu
#SBATCH -N 1
#SBATCH -n 1

module load intel_compiler/2020.2.254 intel_mpi/2020.2.254
srun /home/<your-group>/<your-id>/py38env/bin/pytest -v > pytest.log
```

`pytest.log`に次のようなエラーが出ました。

```
(py38env) $ cd src/gpaw-21.1.0/
(py38env) $ sbatch pytest.sh
(py38env) $ less pytest.log

============================= test session starts ==============================
platform linux -- Python 3.8.12, pytest-6.2.2, py-1.10.0, pluggy-0.13.1 -- /home/k0227/k022707/py38env/bin/python3.8
cachedir: .pytest_cache
rootdir: /home/k0227/k022707/GPAW/src/gpaw-21.1.0
collecting ... collected 423 items

gpaw/test/test_AA_enthalpy.py::test_exx_AA_enthalpy SKIPPED (world.s...) [  0%]
gpaw/test/test_Gauss.py::test_Gauss PASSED                               [  0%]
・
・
・
================================ GPAW-MPI stuff ================================
size: 1
=========================== short test summary info ============================
FAILED gpaw/test/test_Hubbard_U.py::test_Hubbard_U - RuntimeError: Could not ...
FAILED gpaw/test/test_Hubbard_U_Zn.py::test_Hubbard_U_Zn - RuntimeError: Coul...
・
・
・
FAILED gpaw/test/xc/test_xcatom.py::test_xc_xcatom - RuntimeError: Could not ...
ERROR gpaw/test/exx/test_kpts.py::test_kpts[EXX] - RuntimeError: Could not fi...
ERROR gpaw/test/exx/test_kpts.py::test_kpts[PBE0] - RuntimeError: Could not f...
ERROR gpaw/test/exx/test_kpts.py::test_kpts[HSE06] - RuntimeError: Could not ...
ERROR gpaw/test/lrtddft/test_1.py::test_finegrid - RuntimeError: Could not fi...
ERROR gpaw/test/lrtddft/test_1.py::test_velocity_form - RuntimeError: Could n...
ERROR gpaw/test/lrtddft/test_1.py::test_singlet_triplet - RuntimeError: Could...
ERROR gpaw/test/lrtddft/test_1.py::test_spin - RuntimeError: Could not find r...
ERROR gpaw/test/lrtddft/test_1.py::test_io - RuntimeError: Could not find req...
ERROR gpaw/test/lrtddft/test_dielectric.py::test_get - RuntimeError: Could no...
ERROR gpaw/test/lrtddft/test_dielectric.py::test_write - RuntimeError: Could ...
ERROR gpaw/test/utilities/test_wannier_ethylene.py::test_ethylene_energy - Ru...
ERROR gpaw/test/utilities/test_wannier_ethylene.py::test_wannier_centers - Ru...
ERROR gpaw/test/utilities/test_wannier_ethylene.py::test_wannier_centers_gpw
= 265 failed, 109 passed, 35 skipped, 1 xfailed, 108 warnings, 13 errors in 787.71s (0:13:07) =
```

なんとなく `#SBATCH -n 128` にして試してみたところ、

```
============================= test session starts ==============================
platform linux -- Python 3.8.12, pytest-6.2.2, py-1.10.0, pluggy-0.13.1 -- /home/k0227/k022707/py38env/bin/python3.8
cachedir: .pytest_cache
rootdir: /home/k0227/k022707/GPAW/src/gpaw-21.1.0
・
・
・
gpaw/test/eigen/test_davidson.py::test_eigen_davidson FAILED             [ 20%]
gpaw/test/eigen/test_keep_htpsit.py::test_eigen_keep_htpsit FAILED       [ 20%]
gpaw/test/ext_potential/test_constant_e_field.py::test_ext_potential_constant_e_field FAILED [ 20%]
gpaw/test/ext_potential/test_external.py::test_ext_potential_external FAILED [ 21%]
gpaw/test/ext_potential/test_external_pw.py::test_ext_potential_external_pw FAILED [ 21%]
gpaw/test/ext_potential/test_harmonic.py::test_ext_potential_harmonic FAILED [ 21%]
collecting ... collecting ... collecting ... collecting ... collecting ... collecting ... collecting
 ... collecting ... collecting ... collecting ... collecting ... collecting ... collecting ... colle
cting ... collecting ... collecting ... collecting ... collecting ... collecting ... collecting ...
collecting ... collecting ... collecting ... collecting ... collecting ... collecting ...

```

のように途中で止まってしまいます。