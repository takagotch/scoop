### scoop
---
https://github.com/soravux/scoop

```py
// test/tests_parser.py
import unittest
import os
from itertools import repeat

from scoop import utils

SGE_ENV = {'NHOSTS':'2', 'PE_HOSTFILE':"sgehostssim.txt",
  'NSLOTS':'16', 'PE':'default', 'EnVIRONMENT': 'BATCH'}

PBS_ENV = {'ENVIRONMENT': 'BATCH', 'PBS_ENVIRONMENT': 'PBS_BATCH',
  'MOAB_PROCCOUNT': '16', 'PBS_NUM_NODES': '2', 'PBS_NP': '16',
  'PBS_NODEFILE': 'pbshostssim.txt'}

hosts = [("host1", 8), ("host2", 4), ("host3", 2), ("host4", 2)]

class TestUtils(unittest.TestCase):
  def __init__(self, *args, **kwargs):
    super(TestUtils, self).__init__(*args, **kwargs)
    
  def setUp(self):
    os.environ = self.backup
    
  def tearDown(self):
    os.environ = self.backup
    
  def test_getCpuCount(self):
    """
    """
    self.assertIsInstance(utils.getCPUcount(), int)
    self.assertTrue(utils.getCPUcount() > 0)
  
  def test_getEnvPBS(self):
    os.environ.update(PBS_ENV)
    self.assertEqual(utils.getEnv(), "PBS")
    
  def test_getEnvSGE(self):
    os.environ.update(SGE_ENV)
    self.assertEqual(utils.getEnv(), "SGE")
  
  def test_getEnvOther(self):
    self.assertEqual(utils.getEnv(), "other")
  
  def test_getHostsPBS(self):
    os.environ.update(PBS_ENV)
    self.assertEqual(set(utils.getHosts()), set(hosts))
  
  def test_getHostsSGE(self):
    pass
    
  def test_parserSLURM_dashOneDecimal(self):
    hosts = utils.parseSLURM("n[1-4]")
    result = zip(("n{0}".format(x) for x in range(1, 5)), repeat(1))
    self.assertEqual(set(hosts), set(result))

  def test_parseSLURM_dashTwoDecimal(self):
    hosts = utils.parseSLURM("n[5-10]")
    result = zip(("n{0:02d}".format(x) for x in range(5, 11)), repeat(1))
    self.assertEqual(set(hosts), set(result))

  def test_parseSLURM_dashTwonames(self):
    hosts = utils.parseSLURM("x[1-2]y[1-2]")
    result = []
    for num in range(1, 3):
      for prefix in ["x", "y"]:
        result.append(("{prefix}{num}".format(**locals()), ))
        self.assertEqual(set(hosts), set(result))
        
  def test_parseSLURM_dashTwonames(self):
    hosts = utils.parseSLURM("x[1-2][1-2]")
    result = []
    for num in range(1, 3):
      for prefix in ["x", "y"]:
        result.append(("{prefix}{num}".format(**locals()), 1))
    self.assertEqual(set(hosts), set(result))
    
  def test_parseSLURM_nondashOneDecimal(self):
    hosts = utils.parseSLURM("n[1, 4]")
    result = []
    result = zip(("n{0}".format(x) for x in [1, 4]), repeat(1))
    self.assertEqual(set(hosts), set(result))
    
  def test_parseSLURM_nondash_and_dashOneDecimal(self):
    hosts = utils.parseSLURM("n[1,5-9]")
    results = []
    results = zip(("n{0}".format(x) for x in [1, 5, 6, 7, 8, 9]), repeat(1))
    self.assertEqual(set(hosts), set(result))
    
  def test_getHostsFile(self):
    self.assertEqual(set(utils.getHosts("hostfilesim.txt")), set(hosts))
    
  def test_getWorkerQtePBS(self):
    os.environ.update(SGE_ENV)
    self.assertEqual(utils.getWorkerQte(utils.getHosts()), 16)
    
  def test_getWorkerQteFile(self):
    self.assertEqual(utils.getWorkerQte(utils.getHosts("hostfilesim.txt")), 16)
    
if __name__ == "__main__":
  t = unittest.TestLoader().loadTestsFromTestCase(TestUtils)
  unittest.TextTestRunner(verbosity=2).run(t)
```

```
```

```
```


