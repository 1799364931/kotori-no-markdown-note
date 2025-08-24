bustub

p4

- [`TableHeap`](https://github.com/cmu-db/bustub/blob/master/src/include/storage/table/table_heap.h): `MakeIterator`, `GetTuple`, `GetTupleMeta`, `UpdateTupleMeta`, `UpdateTupleInPlace`, `MakeIterator`, `MakeEagerIterator` (and for [Bonus Task 1](https://15445.courses.cs.cmu.edu/fall2024/project4/#bonus1) and beyond, everything with `Lock`).
- [`Tuple`](https://github.com/cmu-db/bustub/blob/master/src/include/storage/table/tuple.h): `SetRid`, `GetRid`, additional `Tuple` constructors, `Empty`, `IsTupleContentEqual`, `GetValue`.
- [`Value`](https://github.com/cmu-db/bustub/blob/master/src/include/type/value.h): `ValueFactory::Get____`, `ValueFactory::GetNullValueByType`, `CompareExactlyEquals`.
- [`Schema`](https://github.com/cmu-db/bustub/blob/master/src/include/catalog/schema.h): `GetColumn`, `GetColumnCount`.
- [`TransactionManager`](https://github.com/cmu-db/bustub/blob/master/src/include/concurrency/transaction_manager.h): `UpdateUndoLink`, `GetUndoLink`[mgrversion中的undolink], `GetUndoLog`, `GetUndoLogOptional`, `UpdateTupleAndUndoLink`[更新tuple 的同时 更新undolink], `GetTupleAndUndoLink`
- [`Transaction`](https://github.com/cmu-db/bustub/blob/master/src/include/concurrency/transaction.h): All member functions are important, as well as the `UndoLink` and `UndoLog` structs.