# IDA错误手册

从 [Failures and troubleshooting (hex-rays.com)](https://hex-rays.com//products/decompiler/manual/failures.shtml) 下载而来

<div class="main">
            
            <div class="container block-doc">
                <!--#include virtual="_header.shtml" -->

<p>
The following failure categories exist:
</p>
<ol>
<li>a crash or access violation
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#interr">internal consistency check failure (interr)</a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#grace">graceful failure to decompile a function</a>
</li><li>incorrect output text
</li><li>inefficient/unclear/suboptimal output text
</li></ol>

<p>
The current focus is on producing a correct output for any correct function.
The decompiler should not crash, fail, or produce incorrect output for a valid input.
Please file a <a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#report">bugreport</a> if this happens.
</p>
<p>
The decompiler has an extensive set of internal checks and assertions.
For example, it does not produce code which dereferences a "void*" pointer.
On the other hand, the produced code is not supposed to be compilable and many
compilers will complain about it. This is a deliberate choice of not making the output
100% compilable because the goal is not to recompile the code but to let humans analyze it faster.
</p>
<p>
The decompiler uses some C++ constructs in the output text.
Their use is restricted to constructs which cannot be represented in C
(the most notable example is passing structures to functions by value).
</p>

<h3><a name="interr">Internal errors</a></h3>
<p>
When the decompiler detects an internal inconsistency, it displays a message
box with the error code. It also proposes you to send the database to the hex-rays.com
server:
</p>

<p>

It is really difficult (almost impossible) to reproduce bugs without a sample database,
so please send it to the server. To facilitate things, the decompiler saves its internal state to
the database, which is really handy if the error occurs after hours and hours of decompilation.
</p>
<p>
It is impossible to decompile anything after an internal error. Please reload the database,
or better, restart IDA.
</p>
<h3><a name="grace">Graceful failures</a></h3>

<p>
When the decompiler gracefully fails on a function, it will display one of the following
messages. In general, there is no need to file a bugreport about a failure except
if you see that the error message should not be displayed.
</p>
<ul>
<li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#01">cannot convert to microcode                </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#02">not enough memory                          </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#03">invalid basic block                        </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#04">positive sp value has been found           </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#05">prolog analysis failed                     </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#06">switch analysis failed                     </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#07">exception analysis failed                  </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#08">stack frame is too big                     </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#09">local variable allocation failed           </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#10">16-bit functions are not supported         </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#11">call analysis failed                       </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#12">function frame is wrong                    </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#13">undefined or illegal type                  </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#14">inconsistent database information          </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#15">wrong basic type sizes in compiler settings</a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#16">redecompilation has been required          </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#17">could not compute fpu stack states         </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#19">max recursion depth reached during lvar allocation</a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#20">variables would overlap                    </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#21">partially initialized variable             </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#22">too complex function                       </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#23">no license available                       </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#24">only 32-bit functions can be decompiled for the current database</a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#25">only 64-bit functions can be decompiled for the current database</a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#26">already decompiling a function             </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#27">far memory model is supported only for pc  </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#28">special segments cannot be decompiled      </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#29">too big function                           </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#30">bad input ranges                           </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#31">current architecture is not supported      </a>
</li><li><a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#32">bad instruction in the delay slot          </a>
</li></ul>

<p>
Please read the <a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#trouble">Troubleshooting</a> section about the possible actions.
</p>
<p><b><a name="01">cannot convert to microcode</a></b></p>
<blockquote>
        This error means that the decompiler could not translate
        an instruction at the specified address into microcode.
        Please check the instruction and its length. If it looks like a regular
        instruction used in the compiler generated code and its length is correct,
        file a bugreport.
</blockquote>

<p><b><a name="02">not enough memory</a></b></p>
<blockquote>

        The error message is self-explanatory. While it should not
        happen very often, it still can be seen on functions with huge
        stacks. No need to report this bug. Hopefully the next version
        will handle functions with huge stack more efficiently.
<p></p>
        Please restart IDA after this error message.
</blockquote>

<p><b><a name="03">invalid basic block</a></b></p>
<blockquote>

        This error means that at the specified address there is a basic block,
        which does not end properly. For example, it jumps out of the
        function, ends with a non-instruction, or simply contains garbage.
        If you can, try to correct the situation by modifying the function
        boundaries, creating instructions, or playing with function tails.
        Usually this error happens with malformed functions.
<p></p>
        If the error happens because of a call, which does not return,
        marking the called function as "noret" will help. If the call
        is indirect, adding a cross reference to a "noret" function will
        help too.
<p></p>
        If this error occurs on a database created by an old version of IDA,
        try to reanalyze the program before decompiling it. In general, it
        is better to use the latest version of IDA to create the databases for decompilation.
<p></p>
        Unrecognized table jumps may lead to this failure too.
</blockquote>

<p><b><a name="04">positive sp value has been found</a></b></p>
<blockquote>

        The stack pointer at the specified address is higher than the initial
        stack pointer. Functions behaving so strangely cannot be decompiled.
        If you see that the stack pointer values are incorrect, modify them
        with the <b>Alt-K (Edit, Functions, Change stack pointer)</b> command in IDA.
</blockquote>

<p><b><a name="05">prolog analysis failed</a></b></p>
<blockquote>

        Analysis of the function prolog has failed. Currently there is not much
        you can do but you will not see this error very often.
        The decompiler will try to produce code with prolog instructions
        rather than stopping because of this failure.
</blockquote>

<p><b><a name="06">switch analysis failed</a></b></p>
<blockquote>

        The switch idiom (an indirect jump) at the specified address could not
        be analyzed. You may specify the switch idiom manually using
        <b>Edit, Other, Specify switch idiom</b>.
<p></p>
        If this error occurs on a database created by an old version of IDA,
        try to delete the offending instruction and recreate it. Doing so
        will reanalyze it and might fix the error because newer versions of IDA handle
        switches much better than older versions.
</blockquote>

<p><b><a name="07">exception analysis failed</a></b></p>
<blockquote>

        This error message should not occur because the current version
        will happily decompile any function and just ignore any exception
        handlers and related code.
</blockquote>

<p><b><a name="08">stack frame is too big</a></b></p>
<blockquote>

        Since the stack analysis requires lots of memory, the decompiler
        will refuse to handle any function with the unaliased stack bigger than 1 MB.
</blockquote>

<p><b><a name="09">local variable allocation failed</a></b></p>
<blockquote>

        This error message means that the decompiler could not allocate local
        variables with the registers and stack locations. You will see this error
        message only if you have enabled HO_IGNORE_OVERLAPS in the
        <a href="https://hex-rays.com//products/decompiler/manual/config.shtml#HO_IGNORE_OVERLAPS">configuration file</a>. If overlapped variables
        are allowed in the output, they are displayed in red.
        <p></p>
        Please check the prototypes of all involved functions, including the
        current one. Variables types and definitions may cause this error too.
        <p></p>
        Updating the function stack frame and creating correct stack variables
        too may help solve the problem.
        <p></p>
        If you got this error after some manipulations with the function type
        or variable types, you may reset the information about the current
        function (<a href="https://hex-rays.com//products/decompiler/manual/interactive.shtml#07">Edit, Other, Reset decompiler information</a>) and start afresh.
</blockquote>

<p><b><a name="10">16-bit functions are not supported</a></b></p>
<blockquote>

        The message text says it all. While the decompiler itself can be fine tuned to
        decompile 16-bit code, this is not a priority.
</blockquote>

<p><b><a name="11">call analysis failed</a></b></p>
<blockquote>
        This is the most painful error message but it is also something you can
        do something about. In short, this message means that the decompiler could
        not determine the calling convention and the call parameters. If this is
        a direct non-variadic call, you can fix it by specifying the callee
        type: just jump to the callee and hit <b>Y</b> to specify the type. For variadic
        functions too it is a good idea to specify the type, but the call
        analysis could still fail because the decompiler has to find out the actual
        number of arguments in the call.
        We would recommend to start by checking the stack pointer in the whole function.
        Get rid of any incorrect stack pointer values. Second, check the types
        of all called functions. If the type of a called function is wrong, it can
        interfere with other calls and lead to a failure. Here is a small example:
<p></p>
<div class="pre">
          push eax
          push edx
          push eax
          call  f1
          call  f2
</div>
<p></p>
        If f1 is defined as a <b>__stdcall</b> function of 3 arguments, and f2 is a function
        of 1 argument, the call analysis will fail because we need in total 4 arguments
        and only 3 arguments are pushed onto the stack.
<p></p>
        If the error occurs on an indirect call, please
        <a href="https://hex-rays.com/products/ida/support/idadoc/1601.shtml">specify the operand type</a>
        of the call instruction. Also,
        <a href="https://hex-rays.com/products/ida/support/idadoc/592.shtml">adding an xref</a> to
        a function of the desired type from the call instruction will work.
        The decompiler will use the type of the referenced function.
<p></p>
        If all input types are correct and the stack pointer values are correct
        but the decompiler still fails, please file a bugreport.
</blockquote>

<b><a name="12">function frame is wrong</a></b>
<blockquote>

        This is a rare error message. It means that something is wrong with the
        function stack frame. The most probable cause is that the return address
        area is missing in the frame or the function farness (far/near) does not
        match it.
</blockquote>

<b><a name="13">undefined or illegal type</a></b>
<blockquote>

        This error can occur if a reference to a named type (a typedef) is made but
        the type is undefined. The most common case is when a type library (like vc6win.til)
        is unloaded. This may invalidate all references to all types defined in it.
        <p></p>
        This error also occurs when a type definition is illegal or incorrect.
        To fix an undefined ordinal type, open the local types windows (Shift-F1)
        and redefine the missing type.
</blockquote>

<b><a name="14">inconsistent database information</a></b>
<blockquote>

        Currently this error means that the function chunk information is incorrect.
        Try to redefine (delete and recreate) the function.
</blockquote>

<b><a name="15">wrong basic type sizes in compiler settings</a></b>
<blockquote>
        Some basic type sizes are incorrect. The decompiler requires that
        <ul>
           <li>sizeof(int) == 4
           </li><li>sizeof(enum) == 4
        </li></ul>
        Please check the type sizes in the Options, Compiler dialog box and
        modify them if they are incorrect.
        <p></p>
        Also ensure that the correct memory model is selected: "near data, near code".
        <p></p>
        Finally, the pointer size must be:
        <ul>
        <li>for 32-bit applications use "near 32bit, far 48bit"
        </li><li>for 64-bit applications use "64bit".
        <ul>
</ul></li></ul></blockquote>

<b><a name="16">redecompilation has been required</a></b>
<blockquote>
        This is an internal error code and should not be visible to the end user.
        If it still gets displayed, please file a bugreport.
</blockquote>

<b><a name="17">could not compute fpu stack states</a></b>
<blockquote>
        The decompiler failed to trace the FPU stack pointer. Please check
        the called function types, this is the only thing available for the moment.
        We will introduce workarounds and corrective commands in the future.
        For more information about floating point support, please follow
        <a href="https://hex-rays.com//products/decompiler/manual/fpu.shtml">link</a>.
</blockquote>

<b><a name="19">max recursion depth reached during lvar allocation</a></b>
<blockquote>
        Please file a bugreport, normally this error message should not be displayed.
</blockquote>

<b><a name="20">variables would overlap</a></b>
<blockquote>
        This is a variant of the <a href="https://hex-rays.com//products/decompiler/manual/failures.shtml#09">variable allocation failure</a> error.
        You will see this error message only if you have enabled
        HO_IGNORE_OVERLAPS in the <a href="https://hex-rays.com//products/decompiler/manual/config.shtml#HO_IGNORE_OVERLAPS">configuration file</a>.
        If overlapped variables are allowed in the output, they are displayed in red.
</blockquote>

<b><a name="21">partially initialized variable</a></b>
<blockquote>
        A partially initialized variable has been detected. Wrong stack trace
        can induce this error, please check the stack pointer.
</blockquote>

<b><a name="22">too complex function</a></b>
<blockquote>
        The function is too big or too complex. Unfortunately there is nothing
        the user can do to avoid this error.
</blockquote>

<b><a name="23">no license available                       </a></b>
<blockquote>
        IDA could not locate your decompiler license.
</blockquote>

<b><a name="24">only 32-bit functions can be decompiled for the current database</a></b>
<blockquote>
        This error message will not currently be displayed.
</blockquote>

<b><a name="25">only 64-bit functions can be decompiled for the current database</a></b>
<blockquote>
        IDA64 can currently decompile only 64-bit functions. To decompile
        32-bit functions please use IDA32.
</blockquote>

<b><a name="26">already decompiling a function             </a></b>
<blockquote>
        An attempt to decompile a function while decompiling another function
        has been detected. Currently only one function can be decompiled at once.
</blockquote>

<b><a name="27">far memory model is supported only for pc  </a></b>
<blockquote>
        Please check the data and code memory models in the Options, Compiler dialog.
        If necessary, reset them to 'near' models.
</blockquote>

<b><a name="28">special segments cannot be decompiled      </a></b>
<blockquote>
        The current function belongs to a special segment (e.g. "extern" segment).
        Such segments do not contain any real code, they contain just pointers
        to imported functions. The function body is located in some other
        dynamic library. Therefore, there is nothing that we could
        decompile.
</blockquote>

<b><a name="29">too big function                           </a></b>
<blockquote>
        The current function is bigger than the maximal permitted size.
        The maximal permitted size is specified by the <a href="https://hex-rays.com//products/decompiler/manual/config.shtml#MAX_FUNCSIZE">MAX_FUNCSIZE</a>
        configuration parameter.
</blockquote>

<b><a name="30">bad input ranges                           </a></b>
<blockquote>
        The specified input ranges are wrong. The range vector cannot be empty.
        The first entry must point to an instruction. Ranges may not overlap.
        Ranges may not start or end in the middle of an item.
</blockquote>

<b><a name="31">current architecture is not supported      </a></b>
<blockquote>
        The current processor bitness, endianness, or ABI settings in the
        compiler options are not acceptable. See the current ABI limitations
        <a href="https://hex-rays.com//products/decompiler/manual/limit.shtml">here</a>.
</blockquote>

<b><a name="32">bad instruction in the delay slot          </a></b>
<blockquote>
        Branches and jumps are not allowed in a delay slot. Such
        instructions signal an exception and cannot be decompiled.
</blockquote>

<h3><a name="trouble">Troubleshooting</a></h3>

<p>
When the decompiler fails, please check the following things:
</p>
<ul>
<li>    <span class="checkpoint">the function boundaries</span>. There should not be any wild branches jumping
        out of function to nowhere. The function should end properly, with a
        return instruction or a jump to the beginning of another function.
        If it ends after a call to a non-returning function, the callee must
        be marked as a <b>non-returning</b> function.

</li><li>    <span class="checkpoint">the stack pointer values</span>. Use the <b>Options, General, Stack pointer</b> command
        to display them in a column just after the addresses in the disassembly view.
        If the stack pointer value is incorrect at any location of the function,
        the decompilation may fail. To correct the stack pointer values, use
        the <b>Edit, Functions, Change stack pointer</b> command.

</li><li>    <span class="checkpoint">the stack variables</span>. Open the stack frame window
        with the <b>Edit, Functions, Stack variables...</b> command and verify that the
        definitions make sense. In some cases creating a big array or a structure variable
        may help.

</li><li>    <span class="checkpoint">the function type</span>. The calling convention, the numbers and the types of the arguments
        must be correct. If the function type is not specified, the decompiler will
        try to deduce it. In some rare cases, it will fail.
        If the function expects its input in non-standard
        registers or returns the result in a non-standard register, you will have
        to inform the decompiler about it. Currently it makes a good guess about the
        non-standard input locations but cannot handle non-standard return locations.

</li><li>    <span class="checkpoint">the types of the called functions and referenced data items</span>. A wrong
        type can wreak havoc very easily. Use the <b>F</b> hotkey to display the
        type of the current item in the message window. For functions, position
        the cursor on the beginning and hit <b>F</b>. If the type is incorrect, modify
        it with <b>Edit, Functions, Set function type</b> (the hotkey is <b>Y</b>).
        This command works not only for functions but also for data and
        structure members.

</li><li>    If a type refers to an <span class="checkpoint">undefined type</span>, the decompilation might fail.

</li><li>    use a database created by <span class="checkpoint">the latest version of IDA</span>.
</li></ul>

<p>
In some cases the output may contain variables in red. It means that local variable
allocation has failed. Please read the page about <a href="https://hex-rays.com//products/decompiler/manual/overvars.shtml">overlapped
variables</a> for the possible corrective methods.
</p>

<p>
The future versions will have more corrective commands but we have to understand
what commands we need.
</p>

<h3><a name="report">Bugreports</a></h3>

<p>
To be useful, the bugreport must contain enough information to reproduce the bug.
The <a href="https://hex-rays.com//products/decompiler/manual/interactive.shtml#08">send database</a> command is the preferred way of sending bugreports
because it saves all relevant information to the database. Some bugs are
impossible to reproduce without this command.
</p><p>
The database is sent in the compressed form to save the bandwidth. An SSL connection is used for the transfer.
</p><p>
If your database/input file is confidential and you cannot send it, try to find a similar file
to illustrate the problem. Thank you.
</p><p>
We handle your databases confidentially (as always in the past).
<!--#include virtual="_footer.shtml" -->

            </p></div>
    
</div>