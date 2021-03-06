# params_proto, a collection of decorators that makes shell argument passing declarative


Now supports both python `3.52` as well as `3.6`! :bangbang::star:

## Todo

### Done
- [x] publish
- [x] add test
- [x] add `python3.52` test on top of `python3.6` test.

## Installation
```bash
pip install params_proto
```

## Usage

### To use a python namespace to declare commandline argments

**Updated** We now use a `Proto` helper function to declare the argparse arguments! See example below:

## Simple example showing how to use python namespace to declare command line arguments:

```python
from .params_proto import cli_parse, is_hidden, Proto, ParamsProto, proto_signature


def test_cli_proto():
    @cli_parse
    class G(ParamsProto):
        """Supervised MAML in tensorflow"""
        npts = Proto(100, help="number of points to sample from distribution")
        num_epochs = Proto(70000, help="number of epochs to train")
        num_tasks = Proto(10, help="number of tasks in the inner loop")
        num_grad_steps = Proto(1, help="number of gradient descent steps in the inner loop")
        num_points_sampled = Proto(10, help="effectively the k-shot")
        eval_grad_steps = Proto([0, 1, 10], help="the grad steps evaluated with full sample")
        fix_amp = Proto(False, help="controls the sampling, fix the amplitude of the sample distribution if True")

    assert G.npts == 100
    G.npts = 10
    assert G.npts == 10
    assert vars(G) == {'npts': 10, 'num_epochs': 70000, 'num_tasks': 10, 'num_grad_steps': 1,
                       'num_points_sampled': 10, 'fix_amp': False,
                       'eval_grad_steps': [0, 1, 10]}
    assert G._proto is not None, '_proto should exist'
```

### Setting Function Signatures using Python Namespace

sometimes, you have a function with wildcard keyword argument signature. It is
annoying to work with such functions because the static type analysis of the
IDE doesn't tell you what needs to go in.

I originally wrote this decorator to help with that case, however the dynamically
set function signature won't show up in the IDE in general. Use this for inspection
purposes if you like.

Below si the usage example and the test case:

```python
def test_proto_signature():
    @cli_parse
    class G(ParamsProto):
        """some parameter proto"""
        n = 1
        npts = Proto(100, help="number of points to sample from distribution")
        ok = True

    @proto_signature(G._proto)
    def main_demo(**kwargs):
        print('npts = ', kwargs['npts'])
        return kwargs['npts']

    # First way is to use proto_signature decorator. The dynamically generated signature
    # however does not show up in pyCharm. It does however, show during run time.
    import inspect

    assert main_demo(npts=10) == 10
    print("main_demo<Function> signature:", inspect.signature(main_demo))
    assert str(inspect.signature(main_demo)) == "(n=1, npts=100, ok=True)"
```

## To Develop

```bash
git clone https://github.com/episodeyang/params_proto.git
cd params_proto
make dev
```

To test, run the following under both python `3.52` and `3.6`.
```bash
make test
```

This `make dev` command should build the wheel and install it in your current python environment. Take a look at the [./Makefile](./Makefile) for details.

**To publish**, first update the version number, then do:
```bash
make publish
```
