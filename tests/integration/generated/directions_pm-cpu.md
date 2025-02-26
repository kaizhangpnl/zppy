# Testing directions for pm-cpu

## Commands to run before running integration tests

### test_bundles

```
rm -rf /global/cfs/cdirs/e3sm/www/forsyth/zppy_test_bundles_www/v2.LR.historical_0201
rm -rf /global/cfs/cdirs/e3sm/forsyth/zppy_test_bundles_output/v2.LR.historical_0201/post
# Generate cfg
python tests/integration/utils.py

# Run first set of jobs:
zppy -c tests/integration/generated/test_bundles_pm-cpu.cfg
# bundle1 and bundle2 should run. After they finish, check the results:
cd /global/cfs/cdirs/e3sm/forsyth/zppy_test_bundles_output/v2.LR.historical_0201/post/scripts
grep -v "OK" *status
# Nothing should print

# Now, invoke zppy again to run jobs that needed to wait for dependencies:
zppy -c tests/integration/generated/test_bundles_pm-cpu.cfg
# bundle3 and ilamb should run. After they finish, check the results:
cd /global/cfs/cdirs/e3sm/forsyth/zppy_test_bundles_output/v2.LR.historical_0201/post/scripts
grep -v "OK" *status
# Nothing should print

# If a final release has just been made, run:
cp /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles expected_bundles_v<version>
```

### test_complete_run

```
rm -rf /global/cfs/cdirs/e3sm/www/forsyth/zppy_test_complete_run_www/v2.LR.historical_0201
rm -rf /global/cfs/cdirs/e3sm/forsyth/zppy_test_complete_run_output/v2.LR.historical_0201/post
# Generate cfg
python tests/integration/utils.py

# Run jobs:
zppy -c tests/integration/generated/test_complete_run_pm-cpu.cfg
# After they finish, check the results:
cd /global/cfs/cdirs/e3sm/forsyth/zppy_test_complete_run_output/v2.LR.historical_0201/post/scripts
grep -v "OK" *status
# Nothing should print

# If a final release has just been made, run:
cp /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_complete_run expected_complete_run_v<version>
```

## Commands to run to replace outdated expected files

### test_bash_generation

```
rm -rf /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bash_files
cd <top level of zppy repo>
# Your output will now become the new expectation.
# You can just move (i.e., not copy) the output since re-running this test will re-generate the output.
mv test_bash_generation_output/post/scripts /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bash_files
# Rerun test
python -u -m unittest tests/integration/test_bash_generation.py
```

### test_bundles

```
rm -rf /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles
# Your output will now become the new expectation.
# Copy output so you don't have to rerun zppy to generate the output.
cp -r /global/cfs/cdirs/e3sm/www/forsyth/zppy_test_bundles_www/v2.LR.historical_0201 /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles
mkdir -p /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles/bundle_files
cp -r /global/cfs/cdirs/e3sm/forsyth/zppy_test_bundles_output/v2.LR.historical_0201/post/scripts/bundle*.bash /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles/bundle_files
cd /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles
# Remove the image check failures, so they don't end up in the expected files.
rm -rf /global/cfs/cdirs/e3sm/www/forsyth/forsyth/zppy_test_bundles_www/v2.LR.historical_0201/image_check_failures
# This file will list all the expected images.
find . -type f -name '*.png' > ../image_list_expected_bundles.txt
cd <top level of zppy repo>
# Rerun test
python -u -m unittest tests/integration/test_bundles.py
```

### test_campaign

```
cd <top level of zppy repo>
chmod u+x tests/integration/generated/update_campaign_expected_files_pm-cpu.sh
./tests/integration/generated/update_campaign_expected_files_pm-cpu.sh
```
This command also runs the test again.
If the test fails on `test_campaign_high_res_v1`, try running the lines of the loop manually:
```
rm -rf /global/cfs/cdirs/e3sm/www/zppy_test_resources/test_campaign_high_res_v1_expected_files
mkdir -p /global/cfs/cdirs/e3sm/www/zppy_test_resources/test_campaign_high_res_v1_expected_files
mv test_campaign_high_res_v1_output/post/scripts/*.settings /global/cfs/cdirs/e3sm/www/zppy_test_resources/test_campaign_high_res_v1_expected_files
```

### test_complete_run

```
rm -rf /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_complete_run
# Your output will now become the new expectation.
# Copy output so you don't have to rerun zppy to generate the output.
cp -r /global/cfs/cdirs/e3sm/www/forsyth/zppy_test_complete_run_www/v2.LR.historical_0201 /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_complete_run
cd /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_complete_run
# Remove the image check failures, so they don't end up in the expected files.
rm -rf /global/cfs/cdirs/e3sm/www/forsyth/forsyth/zppy_test_complete_run_www/v2.LR.historical_0201/image_check_failures
# This file will list all the expected images.
find . -type f -name '*.png' > ../image_list_expected_complete_run.txt
cd <top level of zppy repo>
# Rerun test
python -u -m unittest tests/integration/test_complete_run.py
```

### test_defaults

```
rm -rf /global/cfs/cdirs/e3sm/www/zppy_test_resources/test_defaults_expected_files
mkdir -p /global/cfs/cdirs/e3sm/www/zppy_test_resources/test_defaults_expected_files
# Your output will now become the new expectation.
# You can just move (i.e., not copy) the output since re-running this test will re-generate the output.
mv test_defaults_output/post/scripts/*.settings /global/cfs/cdirs/e3sm/www/zppy_test_resources/test_defaults_expected_files
# Rerun test
python -u -m unittest tests/integration/test_defaults.py
```

## Commands to generate official expected results for a release

### test_bundles

```
cp -r /global/cfs/cdirs/e3sm/www/forsyth/zppy_test_bundles_www/v2.LR.historical_0201 /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles_unified_<#>
mkdir -p /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles_unified_<#>/bundle_files
cp -r /global/cfs/cdirs/e3sm/forsyth/zppy_test_bundles_output/v2.LR.historical_0201/post/scripts/bundle*.bash /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_bundles_unified_<#>/bundle_files
```

### test_complete_run

```
cp -r /global/cfs/cdirs/e3sm/www/forsyth/zppy_test_complete_run_www/v2.LR.historical_0201 /global/cfs/cdirs/e3sm/www/zppy_test_resources/expected_complete_run_unified_<#>
```
